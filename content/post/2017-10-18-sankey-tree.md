---
title: "Visualizing Trees | Sankey + Tree"
date: 2017-10-18T12:00:00-06:00
---

<blockquote class="blockquote-type1">This post is courtesy of <a href="http://displayr.com")>Displayr</a> who have generously offered to sponsor a series of independently authored posts about interactive visualization with R and JavaScript. Thank you so much <a href="http://displayr.com")>Displayr</a> for this opportunity.</blockquote>

[d3](https://d3js.org) provides a helpful suite of [layouts](https://github.com/d3/d3-hierarchy#cluster) (cluster, tree, treemap, pack, and partition) for visualizing tree networks or hierarchies. In addition, we have the [d3-sankey](https://github.com/d3/d3-sankey) plugin for a [Sankey diagram](https://en.wikipedia.org/wiki/Sankey_diagram) as another mechanism for presenting tree data. Approaching the layout decision as a mutually exclusive proposition means that we lose many of the benefits from alternative layouts. However, incorporating our favorite features from more than one layout can provide us with a more effective means of interactive visualization of the very common tree data structure. Let’s explore the specific combination of [d3.tree()](https://github.com/d3/d3-hierarchy#tree) with [d3-sankey](https://github.com/d3/d3-sankey).

## [d3.tree()](https://github.com/d3/d3-hierarchy#tree)

In 1981 [Edward Reingold](http://reingold.co/) and John Tilford published

>Reingold, Edward M., and John S. Tilford. “Tidier drawings of trees.” IEEE Transactions on Software Engineering 2 (1981): 223–228.

which gives us the Reingold-Tilford algorithm, the basis of [d3.tree()](https://github.com/d3/d3-hierarchy#tree). Here is a quick unadorned example of the output of [d3.tree()](https://github.com/d3/d3-hierarchy#tree) with the R dataset Titanic using networkD3 [diagonalNetwork()](https://christophergandrud.github.io/networkD3/#radial) for convenience.

[![d3.tree() Titanic with networkD3 diagonalNetwork()](images/sankeytree_displayr_d3tree.png)](https://app.displayr.com/Dashboard?id=90646047-bce8-48cf-9a7a-24a36b04cb53)

If we look closely, we can see two potential shortcomings. All nodes are assumed to be the same size, and the links do not reveal size.

## [d3-sankey](https://github.com/d3/d3-sankey)

In contrast, [d3-sankey](https://github.com/d3/d3-sankey) helps overcome the two shortcomings of [d3.tree()](https://github.com/d3/d3-hierarchy#tree) mentioned above. In the next [example](https://app.displayr.com/Dashboard?id=2d24a2b8-7ddf-4690-8767-bcde814c59de), we use the same data in a Sankey diagram. We can see that node sizes are no longer uniform and links reveal size.

[![d3-sankey Titanic with networkD3 sankeyNetwork](images/sankeytree_displayr_sankey.png)](https://app.displayr.com/Dashboard?id=2d24a2b8-7ddf-4690-8767-bcde814c59de)

So we “solved” the [d3.tree()](https://github.com/d3/d3-hierarchy#tree) shortcomings, but when we represent as a Sankey diagram, I think we lose the fact that the underlying data is a tree. This “loss of insight” becomes more obvious when we try to think of ways to collapse a Sankey diagram.

## sankeytree

Let’s explore how we might blend [d3-sankey](https://github.com/d3/d3-sankey) with [d3.tree()](https://github.com/d3/d3-hierarchy#tree) and call our concoction a sankeytree.  We can start with [d3.tree()](https://github.com/d3/d3-hierarchy#tree) and then draw our nodes and links to be Sankey-like with height proportional to size. [Displayr](http://displayr.com) has helped us here with their open source R htmlwidget [rhtmlSankeyTree](https://github.com/Displayr/rhtmlSankeyTree). Below is an [example](https://bl.ocks.org/timelyportfolio/34296462d01cc80915d1f01431723763).

[![sankeytree on Titanic](images/sankeytree_sankeytree_titanic.png)](https://bl.ocks.org/timelyportfolio/34296462d01cc80915d1f01431723763)

While not perfect, the height of the flow from parent to child now indicates size of the child in relation to size of the parent. This combined layout still suffers from the constant node height constraint of the Reingold-Tilford algorithm in [d3.tree()](https://github.com/d3/d3-hierarchy#tree) and is much less compact than [d3-sankey](https://github.com/d3/d3-sankey). However, with pan, zoom, collapse, and expand interactivity, we can help overcome the loss of compactness from wasted space. This interactivity also aids user intuition around the tree structure. Here is an [example](https://bl.ocks.org/timelyportfolio/34296462d01cc80915d1f01431723763).

[![sankeytree on Titanic with interactivity](images/sankeytree_sankeytree_titanic.gif)](https://bl.ocks.org/timelyportfolio/34296462d01cc80915d1f01431723763)

## Using sankeytree with Recursive Partitioning

Decision tree learning results in tree structures that offer a more “real-life” opportunity for sankeytree. We can quickly explore a very basic [recursive partitioning](https://cran.r-project.org/web/packages/rpart/vignettes/longintro.pdf) classification on the Titanic data that we have used throughout the post. To do it with no coding, we’ll use Displayr ([live example](https://app.displayr.com/Dashboard?id=e0c54314-c6c9-4f7a-a7c0-a64f35df897a)).

[![Displayr sankeytree on recursive partitioning with Titanic](images/sankeytree_displayr_titanic.gif)](https://app.displayr.com/Dashboard?id=e0c54314-c6c9-4f7a-a7c0-a64f35df897a)

## Next Steps

Interactivity improves the experience and intuition with sankeytree, but the wasted space bothers me. In future posts in the series, we will explore more combinations of sankey features with [d3](https://d3js.org) layouts and extensions to eliminate the wasted space and improve the user experience.