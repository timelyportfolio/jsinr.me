---
title: reactable Visualization of Allocation
author: Kent Russell
date: '2023-12-10'
slug: reactable-allocation
categories: []
tags: []
---

<script src="{{< blogdown/postref >}}index_files/d3/d3.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/core-js/shim.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/react/react.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/react/react-dom.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/reactwidget/react-tools.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/reactable/reactable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/reactable-binding/reactable.js"></script>

In my real job as a portfolio manager, asset allocation is very important, so we monitor on a routine basis. For the weekly update, I used [`reactable`](https://glin.github.io/reactable) with a little custom `d3` JavaScript and SVG to visualize the current allocation compared to investment policy ranges and targets. Below is a quick example with entirely made up (but I think realistic) numbers.

<script>const scalex = d3.scaleLinear().range([40,360])</script>
<div style="width: 800px; background: white;">
<div class="reactable html-widget html-fill-item" id="htmlwidget-1" style="width:auto;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"tag":{"name":"Reactable","attribs":{"data":{"asset":["Fixed Income","Equity","Real Estate","Cash"],"val":[1400000000,750000000,500000000,100000000],"pct":[0.509090909090909,0.272727272727273,0.181818181818182,0.0363636363636364],"target_pct":[0.5,0.25,0.2,0.05],"target_val":[1375000000,687500000,550000000,137500000],"diff":[25000000,62500000,-50000000,-37500000],"min":[0.4,0.2,0.15,0.02],"max":[0.6,0.3,0.25,0.1]},"columns":[{"id":"asset","name":"asset","type":"character","headerStyle":{"text-transform":"uppercase","align-self":"flex-end","font-weight":"normal"},"cell":"\nfunction(cellInfo) {\n  return `\n    <div>\n        ${cellInfo.value}\n    <\/div>\n  `\n}\n      ","header":"function(cellInfo){return `<div><\/div>`}","html":true},{"id":"val","name":"val","type":"numeric","headerStyle":{"align-self":"flex-end","font-weight":"normal","text-align":"center"},"cell":"\nfunction(cellInfo) {\n  if(cellInfo.index === 0) {\n    return `<div>${'$ ' + d3.format(',.0f')(cellInfo.value)}<\/div>`\n  } else {\n    return `<div>${d3.format(',.0f')(cellInfo.value)}<\/div>`\n  }\n}\n      ","header":"function(cellInfo){return `<div>$<\/div>`}","html":true},{"id":"pct","name":"pct","type":"numeric","headerStyle":{"align-self":"flex-end","font-weight":"normal","text-align":"center"},"cell":"\nfunction(cellInfo) {\n  if(cellInfo.row.asset === '') return null\n  const height = 50\n  const width = 400\n  const pady = 12\n  const padx = 20\n  return `\n<svg height = ${height} width = ${width}>\n  <rect\n    x='${scalex(cellInfo.row.min)}'\n    y='${pady}'\n    width='${scalex(cellInfo.row.max)-scalex(cellInfo.row.min)}'\n    height='${height-(2*pady)}'\n    fill='#ccc'\n  />\n  <line\n    x1='${scalex(cellInfo.row.target_pct)}'\n    y1='${pady}'\n    x2='${scalex(cellInfo.row.target_pct)}'\n    y2='${height-pady}'\n    stroke='white'\n    stroke-width='2'\n  />\n  <line\n    x1='${scalex(cellInfo.row.pct)}'\n    y1='${pady}'\n    x2='${scalex(cellInfo.row.pct)}'\n    y2='${height-pady}'\n    stroke='#4fb1ff'\n    stroke-width='2'\n  />\n  <text\n    x='${scalex(cellInfo.row.min)-2}'\n    y='${height/2 + 4}'\n    text-anchor='end'\n    style='font-size:11px;'\n  >\n    ${d3.format('.0%')(cellInfo.row.min)}\n  <\/text>\n  <text\n    x='${scalex(cellInfo.row.max)+2}'\n    y='${height/2 + 4}'\n    text-anchor='start'\n    style='font-size:11px;'\n  >\n    ${d3.format('.0%')(cellInfo.row.max)}\n  <\/text>\n  <text\n    x='${scalex(cellInfo.row.target_pct)}'\n    y='${height - pady/2 + 4}'\n    text-anchor='middle'\n    style='font-size:11px;'\n  >\n    ${d3.format('.0%')(cellInfo.row.target_pct)}\n  <\/text>\n  <text\n    x='${scalex(cellInfo.row.pct)}'\n    y='${pady - 4}'\n    text-anchor='middle'\n    fill='#4fb1ff'\n    style='font-size:11px;'\n  >\n    ${d3.format('.0%')(cellInfo.row.pct)}\n  <\/text>\n  <text\n    x='${scalex(1) + 20}'\n    y='${height/2 + 20}'\n    text-anchor='end'\n    fill='${cellInfo.row.pct > cellInfo.row.target_pct ? 'black' : 'red'}'\n    style='font-size:20px;'\n  >\n    ${\n      cellInfo.row.pct > cellInfo.row.target_pct ?\n        '+' + d3.format('.1%')(cellInfo.row.pct - cellInfo.row.target_pct) :\n        d3.format('.1%')(cellInfo.row.pct - cellInfo.row.target_pct)\n    }\n  <\/text>\n<\/svg>\n  `\n}\n      ","header":"function(cellInfo){return `<div>%<\/div>`}","html":true,"width":400},{"id":"target_pct","name":"target_pct","type":"numeric","headerStyle":{"align-self":"flex-end","font-weight":"normal","text-align":"center"},"show":false},{"id":"target_val","name":"target_val","type":"numeric","headerStyle":{"align-self":"flex-end","font-weight":"normal","text-align":"center"},"show":false},{"id":"diff","name":"diff","type":"numeric","headerStyle":{"align-self":"flex-end","font-weight":"normal","text-align":"center"},"show":false},{"id":"min","name":"min","type":"numeric","headerStyle":{"align-self":"flex-end","font-weight":"normal","text-align":"center"},"show":false},{"id":"max","name":"max","type":"numeric","headerStyle":{"align-self":"flex-end","font-weight":"normal","text-align":"center"},"show":false}],"sortable":false,"pagination":false,"borderless":true,"compact":true,"style":{"fontFamily":"sans-serif"},"rowStyle":{"fontSize":"1.25rem","alignItems":"center","borderBottom":"1px solid lightgray"},"dataKey":"ec1ae9535664420570bd4f01046e313f"},"children":[]},"class":"reactR_markup"},"evals":["tag.attribs.columns.0.cell","tag.attribs.columns.0.header","tag.attribs.columns.1.cell","tag.attribs.columns.1.header","tag.attribs.columns.2.cell","tag.attribs.columns.2.header"],"jsHooks":[]}</script>
</div>

### Code

``` r
library(htmltools)
library(d3r)
library(reactable)

# example portfolio allocation data frame
#   in general this will likely come from another source
alloc <- data.frame(
  asset = c("Fixed Income","Equity","Real Estate", "Cash"),
  val = c(1400000000, 750000000, 500000000, 100000000),
  pct = rep(NA,4), # will calculate later
  target_pct = c(0.50,0.25,0.20,0.05),
  target_val = rep(NA,4), # will calculate later
  diff = rep(NA,4), # will calculate later
  min = c(0.40,0.20,0.15,0.02),
  max = c(0.60,0.30,0.25,0.10)
)

# calculate actual percent
alloc$pct <- alloc$val / sum(alloc$val)

# calculate target value
alloc$target_val <- sum(alloc$val) * alloc$target_pct

# calculate actual to target difference
alloc$diff <- alloc$val - alloc$target_val

rt <- reactable(
  alloc,
  pagination = FALSE,
  sortable = FALSE,
  compact = TRUE,
  style = list(fontFamily = "sans-serif"),
  # turn off borders here
  borderless = TRUE,
  rowStyle = list(
    fontSize = "1.25rem",
    alignItems = "center",
    # add back row borders here
    borderBottom = "1px solid lightgray"
  ),
  defaultColDef = colDef(
    headerStyle = "align-self: flex-end; font-weight: normal; text-align:center;"
  ),
  columns = list(
    asset = colDef(
      header = JS("function(cellInfo){return `<div></div>`}"),
      headerStyle = "text-transform:uppercase; align-self:flex-end; font-weight:normal;",
      html = TRUE,
      cell = JS("
function(cellInfo) {
  return `
    <div>
        ${cellInfo.value}
    </div>
  `
}
      ")
    ),
    val = colDef(
      header = JS("function(cellInfo){return `<div>$</div>`}"),
      cell = JS("
function(cellInfo) {
  if(cellInfo.index === 0) {
    return `<div>${'$ ' + d3.format(',.0f')(cellInfo.value)}</div>`
  } else {
    return `<div>${d3.format(',.0f')(cellInfo.value)}</div>`
  }
}
      "),
      html = TRUE
    ),
    pct = colDef(
      header = JS("function(cellInfo){return `<div>%</div>`}"),
      cell = JS("
function(cellInfo) {
  if(cellInfo.row.asset === '') return null
  const height = 50
  const width = 400
  const pady = 12
  const padx = 20
  return `
<svg height = ${height} width = ${width}>
  <rect
    x='${scalex(cellInfo.row.min)}'
    y='${pady}'
    width='${scalex(cellInfo.row.max)-scalex(cellInfo.row.min)}'
    height='${height-(2*pady)}'
    fill='#ccc'
  />
  <line
    x1='${scalex(cellInfo.row.target_pct)}'
    y1='${pady}'
    x2='${scalex(cellInfo.row.target_pct)}'
    y2='${height-pady}'
    stroke='white'
    stroke-width='2'
  />
  <line
    x1='${scalex(cellInfo.row.pct)}'
    y1='${pady}'
    x2='${scalex(cellInfo.row.pct)}'
    y2='${height-pady}'
    stroke='#4fb1ff'
    stroke-width='2'
  />
  <text
    x='${scalex(cellInfo.row.min)-2}'
    y='${height/2 + 4}'
    text-anchor='end'
    style='font-size:11px;'
  >
    ${d3.format('.0%')(cellInfo.row.min)}
  </text>
  <text
    x='${scalex(cellInfo.row.max)+2}'
    y='${height/2 + 4}'
    text-anchor='start'
    style='font-size:11px;'
  >
    ${d3.format('.0%')(cellInfo.row.max)}
  </text>
  <text
    x='${scalex(cellInfo.row.target_pct)}'
    y='${height - pady/2 + 4}'
    text-anchor='middle'
    style='font-size:11px;'
  >
    ${d3.format('.0%')(cellInfo.row.target_pct)}
  </text>
  <text
    x='${scalex(cellInfo.row.pct)}'
    y='${pady - 4}'
    text-anchor='middle'
    fill='#4fb1ff'
    style='font-size:11px;'
  >
    ${d3.format('.0%')(cellInfo.row.pct)}
  </text>
  <text
    x='${scalex(1) + 20}'
    y='${height/2 + 20}'
    text-anchor='end'
    fill='${cellInfo.row.pct > cellInfo.row.target_pct ? 'black' : 'red'}'
    style='font-size:20px;'
  >
    ${
      cellInfo.row.pct > cellInfo.row.target_pct ?
        '+' + d3.format('.1%')(cellInfo.row.pct - cellInfo.row.target_pct) :
        d3.format('.1%')(cellInfo.row.pct - cellInfo.row.target_pct)
    }
  </text>
</svg>
  `
}
      "),
      html = TRUE,
      width = 400
    ),
    target_pct = colDef(show = FALSE),
    target_val = colDef(show = FALSE),
    diff = colDef(show = FALSE),
    min = colDef(show = FALSE),
    max = colDef(show = FALSE)
  )
)

browsable(tagList(
  d3r::d3_dep_v7(),
  tags$script(HTML("const scalex = d3.scaleLinear().range([40,360])")),
  tags$div(
    style = "width: 800px; background: white;",
    rt
  )
))
```
