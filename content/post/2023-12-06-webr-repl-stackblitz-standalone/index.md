---
title: webR REPL Standalone in Stackblitz
author: Kent Russell
date: '2023-12-06'
slug: webr-repl-standalone-stackblitz
categories: []
tags: []
---

# webR Standalone REPL in React + Vite

[webR](https://docs.r-wasm.org/webr/latest/) offers a very neat [REPL](https://webr.r-wasm.org/latest/). I thought it might be interesting/fun to isolate the REPL into a standalone React + Vite project in Stackblitz. Thanks very much to the lead author of `webR` George Stagg who generously [approved](https://github.com/r-wasm/webr/issues/323) this fork of their amazing work.

## Use Cases

I am still trying to think of use cases for this project in Stackblitz. It seems having this readily available for the world to use, share, and embed (with no install) on the web is nice, but with a little imagination I believe we can do even more with this or something like it. For instance, I think we could change to add R script(s) in `public` for them to load in the `webR` virtual filesystem to populate the top left. Maybe we can leverage Bob Rudis' amazing [`pkgtrap`](https://github.com/hrbrmstr/pkgtrap) and brilliant book [Making Portable CLIs With WebR](https://rud.is/books/webr-cli-book/).  Or maybe it is just easier to use the smaller isolated version to learn.  I would love to hear your ideas.

## Embedded Example

Here is how it looks if we embed the Stackblitz project.

<iframe src = "https://stackblitz.com/edit/vitejs-vite-c5r7sn?embed=1&file=README.md" style="border:0;margin:0;width:95vw;height:800px;"/>