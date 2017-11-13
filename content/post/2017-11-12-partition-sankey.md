---
title: "Visualizing Trees | Partition + Sankey"
draft: false
date: 2017-11-13T07:38:10-06:00
---

<blockquote class="blockquote-type1">This post is courtesy of <a href="http://displayr.com")>Displayr</a> who have generously offered to sponsor a series of independently authored posts about interactive visualization with R and JavaScript. Thank you so much <a href="http://displayr.com")>Displayr</a> for this opportunity.</blockquote>

<span style="font-size:0.75em;">
crossposted at <a href="https://buildingwidgets.com/blog">buildingwidgets</a> and <a href="https://medium.com/@timelyportfolio">Medium</a>
</span>

This will be the last post in our iterations in visualizing trees.  In the next post, we'll move on to how we can use the very popular `React` in `R`.

Our first [post](http://www.jsinr.me/2017/10/18/visualizing-trees--sankey---tree/) yielded a [sankeytree concoction](https://app.displayr.com/Dashboard?id=e0c54314-c6c9-4f7a-a7c0-a64f35df897a).

[![sankeytree on Titanic](images/sankeytree_sankeytree_titanic.png)](https://bl.ocks.org/timelyportfolio/34296462d01cc80915d1f01431723763)

Then our second [post](http://www.jsinr.me/2017/10/28/visualizing-trees--partition---tree/) made a more compact parttree.

[![parttree on Titanic with transition](images/parttree_parttree_titanic.gif)](https://bl.ocks.org/timelyportfolio/a6f2f931935025b0476ea6180d348c59)

Although the parttree is more compact, we can compress even further by stacking our link paths to reflect the leaf details and eliminating the leaf nodes.  Then, we can blend in some interactivity to help the user process the visualization and examine the data.

## Eliminating Repetition

At the leaf level of our parttree, the repetition of `"Yes"` and `"No"` could interfere with our ability to compare survival at each level.

![parttree with repetition highlighted](images/partsankey_partree_repetition.png)

Let's see what happens if we instead convey the survival information in our link paths that connect the nodes.  This is similar to a stacked bar or streamgraph.  To accomplish this, we will use [`d3.stack()`](https://github.com/d3/d3/blob/master/API.md#stacks).

[![partsankey static](images/partsankey_partsankey_static.png)](https://bl.ocks.org/timelyportfolio/703a742a50fc17c842396a83e455a712)

## Interactivity

We save some space, but a user might get confused.  For example, the `"Yes"` does not flow through the nodes.  Some interactivity might help clear up the confusion.

[![partsankey interactive](images/partsankey_partsankey_interactive.gif)](https://bl.ocks.org/timelyportfolio/05a607c1730f717dc66b6c6db36a9f9f)

## Next

Iterating through our last three posts demonstrates how creative blending can result in unique representations of tree hierarchies from R or JavaScript.  We'll stop here with our iteration, but we could easily transform this or other visualizations into [`htmlwidgets`](http://htmlwidgets.org) for even easier consumption by R users.

Much of the recent innovation in JavaScript visualization has happened in the newest frameworks, such as [React](https://reactjs.org/) and [Vue](https://vuejs.org/).  We'll see in the next couple of posts how to use these new frameworks in R.