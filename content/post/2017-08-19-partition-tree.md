---
title: "Visualizing Trees | Partition + Tree"
draft: true
date: 2017-08-19T09:38:10-06:00
---

<blockquote class="blockquote-type1">This post is courtesy of <a href="http://displayr.com")>Displayr</a> who have generously offered to sponsor a series of independently authored posts about interactive visualization with R and JavaScript. Thank you so much <a href="http://displayr.com")>Displayr</a> for this opportunity.</blockquote>

In our first attempt at improving hierarchical visualization, we combined [d3.tree()](https://github.com/d3/d3-hierarchy#tree) with [d3-sankey](https://github.com/d3/d3-sankey). Our [sankeytree concoction](https://app.displayr.com/Dashboard?id=e0c54314-c6c9-4f7a-a7c0-a64f35df897a) allows us to convey size or flow from one level to the next while maintaining some sense of the tree, but the sankeytree still suffers from the universal constant node size (height and width).

[![sankeytree on Titanic](images/sankeytree_sankeytree_titanic.png)](https://bl.ocks.org/timelyportfolio/34296462d01cc80915d1f01431723763)

We are left with extra wasted space that possibly distracts from the message of the visualization. In this post we will see if we can eliminate some of this space with [d3.partition()](https://github.com/d3/d3-hierarchy#partition) assisted by [d3.treemap()](https://github.com/d3/d3-hierarchy#treemap). Let’s call this one parttree.

## [d3.partition()](https://github.com/d3/d3-hierarchy#partition)

Partition, or icicle, visualizations fill space much like a diced treemap or side-by-side stacked bar chart. The visualizations are commonly used in debugging and programming optimization. In this context, they are called [flame graphs](http://www.brendangregg.com/flamegraphs.html).

![flame graph from Chrome debugger](images/parttree_icicle_stacktrace.png)

Since we are trying to eliminate some of the wasted space from our sankeytree, let’s see if we might be able to leverage the “space-filling” [d3.partition()](https://github.com/d3/d3-hierarchy#partition). For consistency, let’s continue to use the Titanic dataset from R and create a partition.

[![d3.partition() on Titanic](images/parttree_d3partition.png)](https://bl.ocks.org/timelyportfolio/33f3c4c3c9297fb3540366ab37a5f56e)

While [d3.partition()](https://github.com/d3/d3-hierarchy#partition) efficiently fills the space, these charts in this context do not reveal the hierarchical nature of the underlying data as much as I would like. Also, in my opinion, the above chart is not very inviting or “fun”.

## smaller Nodes with Links

What if we start with [d3.partition()](https://github.com/d3/d3-hierarchy#partition) and then use a node size smaller than the partition-assigned size? Then, we might have some space to draw links like a [d3.tree()](https://github.com/d3/d3-hierarchy#tree) or [d3-sankey](https://github.com/d3/d3-sankey). Seeing is believing, so let’s make the suggested adjustment to the partitioned Titanic and then animate the transformation.

[![parttree on Titanic with transition](images/parttree_parttree_titanic.gif)](https://bl.ocks.org/timelyportfolio/a6f2f931935025b0476ea6180d348c59)

I consider this good progress, and our new parttree imparts a sense of hierarchy with an efficient and compact portrayal of size and flow. I should note that we sprinkled in some assistance from d3.stack() and [d3.treemap()](https://github.com/d3/d3-hierarchy#treemap).
However, the straight angled links might be a little rigid. This can be solved with help from d3.linkHorizontal.

## Finishing Touches

A little curve in our links might be nice. However, just a line with width defined by stroke-width can limit us in ways we might discuss in future posts, so let’s define a path with four points to draw our link.

[![parttree (curved links) on Titanic](images/parttree_parttree_curved.png)](http://blockbuilder.org/timelyportfolio/9a7aa89d3c18f5a436ae50040191d882)

Just imagine if we add proper labels, good color, and interactivity.

## Next

If we like our new creation, then next steps will be to create a more formal d3 layout and then build a reusable chart based on the layout. As mentioned in the post, drawing the links as a path with four points instead of a line with two points will allow us the ability to add even more encoding and information in our links. In the next post, we will explore what we can do with our new powers.