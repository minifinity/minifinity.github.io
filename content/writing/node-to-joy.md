+++
title = "Node to Joy"
description = "Why graph theory?"
date = 2025-05-10
# updated = 2025-05-10
draft = false

[taxonomies]
categories = ["GSoC"]
# tags = ["one", "two", "three"]

[extra]
lang = "en"
toc = true
comment = false
copy = true
outdate_alert = false
outdate_alert_days = 120
math = false
mermaid = false
featured = false
reaction = false
+++

This summer, I'll be working with the [igraph](https://igraph.org/) team on their open-source network analysis library through Google Summer of Code. I'm thrilled for the opportunity to learn from two dedicated mentors and to dive into a topic I've been fascinated by: network science and graph theory.

## Project
The objective is to expand on igraph’s current functionality to more flexibly work with **degree-constrained null models**. [Null models](https://en.wikipedia.org/wiki/Null_model) are useful because they provide a baseline for determining whether graphs of interest display “non-trivial features” – in other words, they offer a way to define a "generic" or "random" graph so observed features in network of interest can be compared against a meaningful reference point. 

When constructing such a null model, it's important to preserve certain properties of the original graph so that comparisons remain relevant. One potential preserved property is the **degree sequence**.

> A **degree sequence** is a list of the number of edges connected to each node in a graph. For example, if a graph has six nodes and they each have 1, 3, 3, 2, 2, and 2 edges connected to them, then the *degree sequence* is [1, 3, 3, 2, 2, 2]. This captures the distribution of "connectedness" across the graph.

Preserving the degree sequence ensures that the null model retains the same distribution of connectivity, making a comparison more meaningful.

### Details

Three functionalities will be extended to work with more graph structures: 
* **Graphicality: can a graph be built from this degree sequence?**

    The first step in working with a degree-constrained null model is checking whether a graph *can* be constructed from a given degree sequence, or checking for "graphicality."

    igraph already includes `is_graphical()` and `is_bigraphical()` functions that support various combinations of edge types (like loops or multi-edges), both directed and undirected graphs, and bipartite graphs. However, there's currently no efficient way to check graphicality for connected graphs. This can be extended from `is_potentially_connected()`, ideally in linear time.

* **Construction: building a graph that matches a degree sequence**
    
    Once we know a graph is possible, the next step is to actually build one.

    igraph’s `realize_degree_sequence()` can already generate graphs across different types (simple, multigraphs, with or without loops), and it has a companion for bipartite graphs. This project will build on that foundation in two ways:

    * Adding support for graphs with loops but no multi-edges (a currently missing case)

    * Implementing improved methods for construction algorithms
    
* **Rewiring: randomizing edges while preserving a degree sequence**
    
    The final step is to generate random graphs that still match the degree sequence of the original. 

    igraph’s current `rewire()` function handles a limited set of graph types and can’t reach all possible graphs with the same in- and out-degree sequences. The plan is to:
    
    * Add support for more combinations of edge types
    * Support rewiring for bipartite graphs while preserving partitions
    * Enable a three-edge swap method for directed graphs to ensure all valid graphs can be reached with equal probability
    * Add flexibility to preserve only in-degree or only out-degree, depending on use case
    * Make it possible to rewire graphs while preserving connectivity, using a more efficient Viger–Latapy method

At its core, null models help researchers ask better questions about networks – not just *what* it looks like, but *why* it looks that way, and whether that is meaningful or expected. By improving how igraph handles degree-constrained null models, I hope to make a small but practical contribution to network science.

> Much of this work was informed by a paper by one of my mentors, Szabolcs Horvát, and Carl D Modes: *“Connectedness matters: construction and exact random sampling of connected
networks.”* (DOI: 10.1088/2632-072X/abced5).

---

## More Thoughts
I can't recall how or why I became interested in networks and graphs. My hypothesis is that it stemmed from the abundance of time I had to traverse Wikipedia rabbit holes during the COVID pandemic. 

Often, I'd start on a page that wasn't exactly what I was looking for – maybe the term was too broad or too narrow, or only tangentially related. But unless you were on a page with less than a handful of links, you would eventually find a link to something that felt like it pointed in the right direction, or at least was the closest of the available links to the right direction. 

Maybe I was drawn to the idea that we carry around a kind of "semantic distance" in our heads – a fuzzy intuition for how ideas are related, even if we can't express it precisely. Maybe I just wanted to beat all my friends at the [Wikipedia speedrun](https://wikispeedrun.org/), a shortest-path problem in disguise. Or maybe it all started when I came across [the most aesthetically-arresting graph theoretical analysis](https://youtu.be/-llumS2rA8I?si=choIrC7Ch_6IObbN) explaining [why most Wikipedia pages eventually link back to "Philosophy,"](https://en.wikipedia.org/wiki/Wikipedia:Getting_to_Philosophy) and I was just drunk on the visuals. 

In any case, I began my undergraduate study in computer science largely owing to this interest in graphs, networks, and structural abstractions which, while not the most conventional starting point, drew me into computer science and discrete mathematics in an organic way. There is no shortage of nodes and edges in everyday life. Social media connections link people. Highways systems weave cities. Neurons wire our own thoughts and consciousness. 

Formalities aside, the idea behind networks – that the relationships between things are just as interesting as the things themselves – is one that seemingly underpins both social and natural worlds. The more I learn about them, the more I am awed by their ability to model complexities with such minimal ingredients.