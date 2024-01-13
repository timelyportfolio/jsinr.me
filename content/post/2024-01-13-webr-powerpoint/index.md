---
title: "Powerpoint from Anywhere with officer and webR"
author: "Kent Russell"
date: "2024-01-13"
slug: "webr-powerpoint"
categories: []
tags: []
---

The last [nudge](https://www.jsinr.me/2024/01/10/selfcontained-htmlwidgets/) creating selfcontained html from `webR` on an iPad leads to another idea.  Why not create Powerpoint documents in the same way?  The R community has an unsung hero [David Gohel](https://davidgohel.r-universe.dev/) with [arData](https://www.ardata.fr) who has generously provided us with amazing capabilities.  Say what you want about Microsoft Office, but for many of us Office is an inescapable part of our corporate lives.  Similar to selfcontained html, Powerpoint can be a way to communicate data and information within an organization.

I still find it hard to believe, but almost every R package is now available in `webR` including `officer`.  `webR` + `officer` means that we can create Powerpoint (and Word) anywhere we have a modern browser including an iPad and iPhone.  Below is a quick example of some code that we can run in the `webR` [repl](https://webr.r-wasm.org/latest/) to create a poorly designed but functional Powerpoint document supplemented with all the power of `R`.  In the screenshot below, we can see that our Powerpoint is created and is available with "Download File" in the repl.

> Please know that with a little work and a well designed template quite professional slides can be achieved.

![webR and officer creating Powerpoint in repl](/images/webr_powerpoint.png)


```r
library(officer)
library(lattice) # for plot example

ppt <- officer::read_pptx()

# Title Slide
ppt <- officer::add_slide(ppt, layout = "Title Slide")
ppt <- officer::ph_with(
  ppt,
  "Powerpoint from webR",
  location = ph_location_type(type = "ctrTitle")
)
ppt <- officer::ph_with(
  ppt,
  "using the brilliant officer package by David Gohel",
  location = ph_location_type(type = "subTitle"),
)

# Title and Content with link
ppt <- officer::add_slide(ppt, layout = "Title and Content")
ppt <- officer::ph_with(
  ppt,
  "Brilliant, Amazing, and Well Documented",
  location = ph_location_label("Title 1")
)
ppt <- officer::ph_with(
  ppt,
  "https://ardata-fr.github.io/officeverse/officer-for-powerpoint.html",
  location = ph_location_label("Content Placeholder 2")
)
ppt <- ph_hyperlink(
  ppt,
  ph_label = "Content Placeholder 2",
  href = "https://ardata-fr.github.io/officeverse/officer-for-powerpoint.html"
)

ppt <- officer::add_slide(ppt, layout = "Title and Content")
ppt <- officer::ph_with(
  ppt,
  "Available Anywhere with webr",
  location = ph_location_label("Title 1")
)
ppt <- officer::ph_with(
  ppt,
  c(
    "No local install necessary",
    "Just need Internet and a Browser"
  ),
  location = ph_location_label("Content Placeholder 2")
)

ppt <- officer::add_slide(ppt, layout = "Title and Content")
ppt <- officer::ph_with(
  ppt,
  paste0(capture.output(sessionInfo())[1:3], collapse="\n"),
  location = ph_location_fullsize()
)

# Title and Content with table
# unfortunately flextable depends on curl which is not available for wasm yet
ppt <- officer::add_slide(ppt, layout = "Title and Content")
ppt <- officer::ph_with(
  ppt,
  "Table Example",
  location = ph_location_label("Title 1")
)
ppt <- officer::ph_with(
  ppt,
  value = head(mtcars),
  location = ph_location(top = 1.25, left = 0.5, width = 9, height = 5),
  properties = officer::prop_table(colWidths = table_colwidths(4))
)

# Title and Content with lattice graphic
ppt <- officer::add_slide(ppt, layout = "Two Content")
ppt <- officer::ph_with(
  ppt,
  "Base Graphics",
  location = ph_location_label("Title 1")
)
ppt <- officer::ph_with(
  ppt,
  officer::plot_instr(code = {
    # help(officer::ph_with)
    col <- c("#440154FF", "#443A83FF", "#31688EFF",
             "#21908CFF", "#35B779FF", "#8FD744FF", "#FDE725FF")
    barplot(1:7, col = col, yaxt="n")
  }),
  location = ph_location_label("Content Placeholder 2")
)
ppt <- officer::ph_with(
  ppt,
  "supports base graphics",
  location = ph_location_label("Content Placeholder 3")
)

# Title and Content with lattice graphic
ppt <- officer::add_slide(ppt, layout = "Two Content")
ppt <- officer::ph_with(
  ppt,
  "lattice and ggplot2 ",
  location = ph_location_label("Title 1")
)
ppt <- officer::ph_with(
  ppt,
  officer::plot_instr(code = {
    print(
      lattice::wireframe(volcano, shade = TRUE, aspect = c(61/87, 0.4), light.source = c(10,0,10))
    )
  }),
  location = ph_location_label("Content Placeholder 2")
)
ppt <- officer::ph_with(
  ppt,
  "also supports lattice and ggplot2 and basically everything",
  location = ph_location_label("Content Placeholder 3")
)

# Title and Content with lattice graphic
ppt <- officer::add_slide(ppt, layout = "Two Content")
ppt <- officer::ph_with(
  ppt,
  "Microsoft Office Charts",
  location = ph_location_label("Title 1")
)
ppt <- officer::ph_with(
  ppt,
  "Error: Error in writexl: failed to create workbook",
  location = ph_location_label("Content Placeholder 2")
)
ppt <- officer::ph_with(
  ppt,
  "officer supports Microsoft Office charts, but webr gives an error with mschart",
  location = ph_location_label("Content Placeholder 3")
)

# this should be in /usr/web_home/ and the repl gives us a nice download button
print(ppt, target = "webr_powerpoint.pptx")
```
