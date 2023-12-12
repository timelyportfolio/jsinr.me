---
title: htmlwidgets + webR
author: Kent Russell
date: '2023-12-11'
slug: htmlwidgets-webr-experiment
categories: []
tags: []
---

<script src="{{< blogdown/postref >}}index_files/react/react.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/react/react-dom.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/reactwidget/react-tools.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/reactable/reactable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/reactable-binding/reactable.js"></script>

Early in my `webR` explorations, I started to wonder how we might pair `htmlwidgets` with `webR`.
Your first question is probably

> Why would we want or need `htmlwidgets` in JavaScript if we are fully immersed in JavaScript?

Well, many `htmlwidget` authors carefully constructed their widget to allow an R user to stay
in their cozy R context with well documented arguments avoiding the unfamiliar land of JavaScript dealing with issues like data conversion. For instance [`react-table`](https://tanstack.com/table/v8) is a brilliant JavaScript library, but Greg Lin has done such an amazing job with [`reactable`](https://glin.github.io/reactable) that even as an experienced JavaScript developer I prefer building with the R `htmlwidget`. So now that we have R in JavaScript (still hard for me to believe), we might want to benefit from the convenience of `htmlwidgets`.

Below is one approach to leveraging `htmlwidgets` in `webR` using the technique described in this [post](https://buildingwidgets.com/2016/08/04/widgets-without-r/). We’ll follow these steps:

1.  Manually add dependencies with `htmlwidgets::getDependency()`. Eventually I will see if we can avoid this step and use the `webR` virtual file system to serve up the necessary files.
2.  Initialize `webR`
3.  Create our `reactable` htmlwidget just like we would in `R`
4.  Use the `json` outside of `webR` in our top-level JavaScript to create the `reactable` in our web page

It works, but I’m still left wondering whether anyone besides me would ever want to do this, and if so is there a better way.

<!--container for our future widget-->

<div id="myreactable" style="height: 90vh; width: 100%;">

loading…

</div>

### Code

``` r
library(htmltools)
library(reactable)

tagList(
  # add dependencies for htmlwidget here,
  #   in this case for reactable we will need these
  reactR::html_dependency_react(),
  reactR::html_dependency_reacttools(),
  htmlwidgets::getDependency("reactable","reactable"),
  
  # load and init webr
  tags$script(HTML(
'
(async function() {
  const { WebR, ChannelType } = await import(
    "https://cdn.jsdelivr.net/npm/webr/dist/webr.mjs"
  );
  const webR = new WebR({
    channelType: ChannelType.PostMessage
  });
  await webR.init();
  await webR.installPackages(["reactable"]);
  // construct reactable and then get x component of the widget
  const rt_x = await webR.evalRString(`
    library(reactable)
    library(jsonlite)
    jsonlite::toJSON(reactable(mtcars, pagination=FALSE)$x, auto_unbox = TRUE, force = TRUE)
  `)
  console.log(rt_x);
  let rt_json = JSON.parse(rt_x);
  // also need to parse data in tag since it will be quoted
  rt_json.tag.attribs.data = JSON.parse(rt_json.tag.attribs.data);
  // use HTMLWidgets.widgets to give us
  // a list of available htmlwiget bindings
  //  in this case we should just have one
  // assume there might be lots, so filter for the one we want
  //  in this case, we want reactable
  const rt_binding = HTMLWidgets.widgets.filter(function(widget){
    return widget.name === "reactable"
  })[0];
  // get our htmlwidget DOM element
  const el = document.getElementById("myreactable");
  const instance = rt_binding.initialize(el);
  rt_binding.renderValue(
    el,
    rt_json,
    instance
  );
})()
'
  ))
)
```

<script>
(async function() {
  const { WebR, ChannelType } = await import(
    "https://cdn.jsdelivr.net/npm/webr/dist/webr.mjs"
  );
  const webR = new WebR({
    channelType: ChannelType.PostMessage
  });
  await webR.init();
  await webR.installPackages(["reactable"]);
  // construct reactable and then get x component of the widget
  const rt_x = await webR.evalRString(`
    library(reactable)
    library(jsonlite)
    jsonlite::toJSON(reactable(mtcars, pagination=FALSE)$x, auto_unbox = TRUE, force = TRUE)
  `)
  console.log(rt_x);
  let rt_json = JSON.parse(rt_x);
  // also need to parse data in tag since it will be quoted
  rt_json.tag.attribs.data = JSON.parse(rt_json.tag.attribs.data);
  // use HTMLWidgets.widgets to give us
  // a list of available htmlwiget bindings
  //  in this case we should just have one
  // assume there might be lots, so filter for the one we want
  //  in this case, we want reactable
  const rt_binding = HTMLWidgets.widgets.filter(function(widget){
    return widget.name === "reactable"
  })[0];
  // get our htmlwidget DOM element
  const el = document.getElementById("myreactable");
  const instance = rt_binding.initialize(el);
  rt_binding.renderValue(
    el,
    rt_json,
    instance
  );
})()
</script>
