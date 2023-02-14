---
layout: default
title: Graphs
description: Graphs
has_toc: false
nav_order: 35
parent: OOP
permalink: /oop/35_Graphs
---

background-image: url('../images/graphs/directed_graph_with_6_nodes.svg')
background-size: 45%

# Graphs
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

##  Definition and Terminology 

+ A .term[graph] is a data structure containing nodes and connections between them (much like a tree), but with no requirement for hierarchical ordering.
   + We often call the nodes .term[vertices], and the connections between them .term[edges].
   + A .term[simple graph], then is the non-empty set of vertices and edges $G=(V,E)$.

.center[
![:scale A graph, 300px](../images/graphs/undirected_graph_with_6_nodes.svg)
![:scale A directed graph, 200px](../images/graphs/directed_graph_with_6_nodes.svg)
![:scale A weighted graph, 250px](../images/graphs/undirected_graph_with_weighted_edges.svg)
]


---

##  Terminology 

+ A .term[directed graph] (or "digraph") is the non-empty set of vertices and edges $G=(V,E)$ where the edges $E={v_i, v_j}$ have a direction associated with them.
   + Neither simple graphs nor digraphs allow pairs of vertices to have more than one edge connecting them.  A .term[multigraph] relaxes this restriction.
   + A .term[pseudograph] allows an edge to connect a single vertex to itself (a loop).


.center[![:scale A directed graph, 220px](../images/graphs/directed_graph_with_6_nodes.svg)]

---

##  Terminology 

+ A .term[weighted graph] is a graph in which each edge has a number (or .term[weight]) assigned.
    + Depending on context, weight is also called "cost", "distance", "length", or some other name.

+ A graph is .term[complete] if for each pair of distinct vertices, there is exactly one edge connecting them.

.center[![:scale A weighted graph, 300px](../images/graphs/undirected_graph_with_weighted_edges.svg)]

---

##  Terminology 

+ A .term[subgraph] is a set of edges and vertices that are themselves subsets of the edges and vertices of a larger graph.
    + A subgraph .term[induced by] vertices $V'$ is a graph such that any edges in the subgraph $(V', E')$ are also in the larger graph.
    + Two vertices are .term[adjacent] if there is an edge connecting them that is a member of the same graph.
    + The edge connecting them is said to be .term[incident] with them.

.center[![:scale A Subgraph (in blue), 300px](../images/graphs/subgraph.svg)]

---

##  Terminology 

+ The .term[degree] of a vertex $v$ is the number of edges incident with $v$.

   + A vertex with no incident edges is called an .term[isolated vertex].

      + It is possible to have a graph $G$ containing only isolated vertices.  ($E$ can be empty).

.center[![:scale Vertex Degrees, 300px](../images/graphs/vertex_degrees.svg)]

---

##  Representations 

+ Aside from drawings: Graphs may be represented by
   + .term[Adjacency lists]
   + .term[Adjacency matrices]
   + .term[Incidence matrices]
      + See Drozdek pg 378-379

---

##  Representation: Adjacency List

.footnote[Drozdek, Figure 8.2]

.left-column[
![:scale Graph, 10.5em](../images/graphs/fig_8.2a_graph.svg)

![:scale Adjacency Table, 10.5em](../images/graphs/fig_8.2b_adjacency_list_table.svg)
]

.right-column[![:scale , 11.5em](../images/graphs/fig_8.2c_adjacency_list_logical.svg)]

---

##  Representation: Adjacency matrix

.footnote[Drozdek, Figure 8.2]

.center[![:scale , 260px](../images/graphs/fig_8.2a_graph.svg)]

.center[![:scale , 450px](../images/graphs/fig_8.2d_adjacency_matrix.svg)]

---

##  Representation: Incidence matrix

.footnote[Drozdek, Figure 8.2]

.center[![:scale , 260px](../images/graphs/fig_8.2a_graph.svg)]

.center[![:scale , 490px](../images/graphs/fig_8.2e_incidence_matrix.svg)]

---

##  Traversals 

* Much like tree traversals, vertices are "visited" one at a time.
   
* Unlike trees, graphs have .term[cycles], so tree traversal algorithms would result in infinite loops.
  * A .term[cycle] is a path from one vertex $v_i$ through one or more edges such that the path returns to $v_i$.

* Vertices must be "marked" to avoid re-visiting the same vertex.
* Isolated vertices must also be visited in some way.

---

##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3a_undirected_graph.svg)]

---

##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_1.svg)]

---

##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_2.svg)]

---


##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_3.svg)]

---


##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_4.svg)]

---


##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_5.svg)]

---


##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_6.svg)]

---


##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_7.svg)]

---


##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_8.svg)]

---


##  Depth-First Traversal: Example

+ Starting with the list $V$  of vertices, the next available vertex v is visited
    + Create the list of vertices adjacent to $v$ , and perform depth-first traversal on them.
    + If v has no adjacent vertices, backtrack to the predecessor of $v$ .
    + When you backtrack to the vertex where you started, you are done.
    + Repeat as long as there are unvisited vertices

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_9.svg)]

---

##  Depth-First Traversal: Example

* This algorithm generates a tree (or forest , a set of trees) called a .term[spanning tree].

   * A spanning tree includes all vertices of the original graph, but does not contain any cycles.

.center[![](../images/graphs/fig_8.3b_undirected_graph_depth-first_finished.svg)]

---

##  Breadth-First Traversals

We have discussed Depth-First traversals, where the traversal choose one path and "follows" it as far as possible before returning to try other paths.  

The .term[Breadth-First Traversal] instead focuses on visiting all neighboring vertices before proceeding to other vertices.  

In a tree-like structure, we could visualize this as working across each level of the tree before proceeding further "down" toward the leaves.  

In a graph, the idea is the same - work on all vertices you can discover at the same "level" (distance from the start) before moving further into the graph.

---

## Breadth-First traversals utilize a Queue

Whereas depth-first traversals made use of a _stack_ as the primary "bookkeeping" data structure to allow backtracking, breadth-first traversals will use a _queue_ to aid in prioritizing the ordering of vertex visits.  The general algorithm is:

```text
function breadthFirstSearch(graph G):
    let unseen := all vertices from G
    let edges := empty List {}
    let working := empty Queue {}
    for each vertex v in unseen do
        remove v from unseen
        enqueue v into working
        while working is not empty do
            v := dequeue from working
            for all vertices u adjacent to v in G do
                if u is in unseen
                    enqueue u into working
                    insert edge(vu) into edges
    return edges
```

.footnote[See also: Drozdek pg. 397]

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5a_undirected_graph.svg)]

We'll start traversing from (a).

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_1.svg)]

We see the nodes with edges from (a) and queue them up...

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_2.svg)]

From (e) we don't discover anything new; just mark it and continue.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_3.svg)]


From (f) we don't discover anything new; just mark it and continue.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_4.svg)]


From (g) we discover (b) and add it to the queue.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_5.svg)]


From (i) we don't discover anything new; just mark it and continue.

Notice that the edges (ef), (ei), (fi) will not be traversed at this point.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_6.svg)]


From (b) we don't discover anything new; just mark it and continue.

Now our queue is empty, but we have unvisited nodes, so we pick one ( **(c)** )
and continue.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_7.svg)]


From (b) we discover (h) and add it to the queue.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_8.svg)]


From (h) we discover (d) and add it to the queue.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_9.svg)]


From (d) we don't discover anything new.

Also, our queue is empty now, so we check for unvisited nodes...

But there are none.  That means we are finished.

---

### Breadth-First traversal: Example

.footnote[Drozdek Fig. 8.5]

.center[![](../images/graphs/fig_8.5b_undirected_graph_breadth-first_finished.svg)]

Final state, with visit ordering shown.  

Edges that were traversed are shown in solid black; edges not traversed are shown in dotted grey.

---

##  Dijkstra's Algorithm 

* Let the node at which we are starting be called the _initial node_. Let the distance of node $Y$ be the distance from the initial node to $Y$.  Dijkstra's algorithm will assign some initial distance values and will try to improve them step by step.

---

##  Dijkstra's Algorithm 

(1) Assign to every node a tentative distance value: set it to zero for our initial node and to infinity for all other nodes.

(2) Mark all nodes except the initial node as unvisited. Set the initial node as current. Create a set of the unvisited nodes called the unvisited set consisting of all the nodes except the initial node.

---

##  Dijkstra's Algorithm 

(3) For the current node, consider all of its unvisited neighbors and calculate their tentative distances.
* For example, if the current node $A$ is marked with a distance of 6, and the edge connecting it with a neighbor $B$ has length 2, then the distance to $B$ (through $A$) will be $6+2=8$. 
* If this distance is less than the previously recorded distance, then overwrite that distance. Even though a neighbor has been examined, it is not marked as visited at this time, and it remains in the unvisited set .

---

##  Dijkstra's Algorithm 

(4) When finished considering the neighbors of current node, mark current node as visited and remove it from the unvisited set . A visited node will never be checked again; its distance recorded now is final and minimal.
* The next current node will be the node marked with the lowest (tentative) distance in the unvisited set .
* If the unvisited set is empty, then stop. The algorithm has finished. Otherwise, set the unvisited node marked with the smallest tentative distance as the next "current node" and go back to step (3).

---

##  Dijkstra's Algorithm 


```text
function Dijkstra(Graph, source):
    let Q be an empty queue
    for each vertex v in Graph.Vertices:  # Initialize
        dist[v] ← INFINITY
        prev[v] ← UNDEFINED
        add v to Q
    dist[source] ← 0
    
    while Q is not empty:
        u ← vertex in Q with min dist[u]
        remove u from Q
        
        for each neighbor v of u still in Q:
            alt ← dist[u] + Graph.Edges(u, v)
            if alt < dist[v]:
                dist[v] ← alt
                prev[v] ← u
    return dist[], prev[]
```

.footnote[Source: <https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm>]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_0.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_1.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_2.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_3.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_4.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_5.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_6.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_7.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_8.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_9.svg)]

---

.footnote[Drozdek, Figure 8.7]

## Dijkstra's Algorithm Example

.center[![](../images/graphs/fig_8.7_weighted_digraph.svg)]

.center[![](../images/graphs/fig_8.7b_Dijkstra_10.svg)]

You can find the shortest path by backtracking to the points where the "best"
assigned distance updated to the final value.

---

##  Ford's Algorithm (Bellman-Ford algorithm)

Dijkstra's Algorithm does not work if some weights are negative.

To solve this problem, a _label-correcting_ algorithm can be used.

Ford's algorithm is an example.

* Does not permanently determine the shortest distance for any vertex until it processes the entire graph.

---

##  Ford's Algorithm (Bellman-Ford algorithm)


```text
function BellmanFord(list vertices, list edges, vertex source) is
    distance := list of size n
    predecessor := list of size n

    for each vertex v in vertices do // Step 1: initialize graph
        distance[v] := inf           // Init. vertex dist. to infinity
        predecessor[v] := null       // Init. a null predecessor
    
    distance[source] := 0            // Dist. to self is zero.

    repeat |V|−1 times:              // Step 2: relax edges repeatedly
         for each edge(u, v) with weight w in edges do
             if distance[u] + w < distance[v] then
                 distance[v] := distance[u] + w
                 predecessor[v] := u

                                     // Step 3: error if neg. weight cycle
    if a negative-weight cycle exists, raise an error 

    return distance, predecessor
```

.footnote[Source: <https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm>]


---

##  General Label-Correcting Algorithm 

.footnote[Based on Drozdek, Chapter 8.3]
.small-90[
```text
function labelCorrectingAlgorithm (weighted simple digraph Graph, vertex first)
    for all vertices v in Graph do      // Step 1: initialize graph
        currDist[v] := inf              // Init. vertex dist. to infinity
    currDist[first] := 0                // Dist. to self is zero
    toBeChecked := {first}              // Set of vertices to check
    while toBeChecked is not empty      // Step 2: relax edges repeatedly
        v := a vertex in toBeChecked
        remove v from toBeChecked
        for all vertices u adjacent to v do
            if currDist[u] > currDist[v] + weight(edge(vu))
                currDist[u] := currDist[v] + weight(edge(vu))
                predecessor[u] := v
                add u to toBeChecked if it is not there
```
]
Efficiency hinges on the data structured used for `toBeChecked`

---

##  All-To-All Shortest PathsThe WFI Algorithm 

.footnote[Based on Drozdek, Chapter 8.3]

```text
function WFI(matrix weights):
   for i := 1 to numberOfVertices do
      for j := 1 to numberOfVertices do
         for k := 1 to numberOfVertices do
            if weights[j][k] > weights[j][i] + weights[i][k] then
               weights[j][k] := weights[j][i] + weights[i][k] then         
   return weights
```

* `weights` is an adjacency matrix

* N-cubed efficiency...

* Can detect cycles if the diagonal of the weight matrix is initialized to infinity.

---

##  Cycle Detection 

We often need to detect cycles in both directed and undirected graphs.

Depth-First Traversalcan be used for this.

---

##  Cycle Detection: Undirected Graphs

```text
function detectCyclesDFS(vertex v, Graph G):
    let static visited := {}   // Set of visited nodes (shared between calls)
    let static edges   := {}   // Set of edges found (shared for all calls)
    let has_cycle := false
    insert v into visited
    for each vertex u in G adjacent to v do
        if u is not in visited:
            has_cycle := has_cycle || detectCyclesDFS(u, G)
        else if edge(u,v) is not in edges:
            has_cycle := true  // cycle detected
        add edge(u,v) to edges
    return has_cycle
```

---

##  Cycle Detection: Digraphs 

Algorithm is a bit more complicated.  Label vertices as "working" when we 
discover them, and as "finished" when we exhaust recursively traversing all 
adjacent nodes.  

If we see a vertex labeled "working" for the second time, we have a cycle.

```text
function detectDigraphCyclesDFS(vertex v, Graph G):
    let has_cycle := false
    label[v] = WORKING         // label the current vertex as "working"
    for each vertex u in G adjacent to v do
        if u has no label yet:
            has_cycle := has_cycle || detectDigraphCyclesDFS(u, G)
        else if label[u] is not FINISHED
            has_cycle := true  // cycle detected
    num[v] = FINISHED          // mark v as "finished"
    return has_cycle
```

---

##  Eulerian Graphs 

.small[_(Euler is pronounced very much like "oiler".)_]

* An .trail[Eulerian trail] is a path that includes all edges of the graph only once.

* An .trail[Eulerian cycle] is a cycle that is also an Eulerian trail.

* An .trail[Eulerian graph] is a graph that has an Eulerian cycle.
    * A graph is Eulerian if every vertex is incident to an even number of edges.

---

##  Finding Eulerian Cycles : Fleury's Algorithm

.green["_Only take a bridge when there is no other path to take._"]

```text
function FleuryAlgorithm(undirected graph G):
    let success := false
    let v := any vertex in G
    let path := v
    let untraversed := G
    while v has untraversed edges
        let e represent an edge to be determined later
        if edge(v,u) is the only untraversed edge
            e := edge(v,u)
            remove v from untraversed
        else e := edge(v,u) which is not a bridge in untraversed
        path := path + u
        remove e from untraversed
        v := u
    if untraversed has no edges
        success := true
    return success, path
```

---

## Eularian graph and cycle

.footnote[Drozdek, Figure 8.34]

.left-column[
.small-80[.center[.blue[
<br>
<br>
An Eulerian graph
]]]

.center[![:scale Eulerian Graph, 11em](../images/graphs/fig_8.34_a_Eulerian_graph.svg)]
]

.right-column[
.small-80[.center[.blue[
An Eulerian path illustrated (numbers indicate traversal order from **b**)
]]]

.center[![:scale Eulerian Path, 11em](../images/graphs/fig_8.34_c_Eulerian_path.svg)]
]

---

##  Hamiltonian Graphs

* A .term[Hamiltonian cycle] is a cycle that passes through all the vertices of the graph.
   * A .term[Hamiltonian graph] is a graph that includes at least one Hamiltonian cycle.
   * All complete graphs are Hamiltonian.
      * As a consequence, an algorithm of first create a complete graph using false edges, then reducing it can be used to find Hamiltonian cycles in incomplete graphs.

.center[![:scale Hamiltonian Graph, 11em](../images/graphs/Hamiltonian_graph.svg)]

.footnote[Image: https://commons.wikimedia.org/wiki/File:Hamiltonian_path.svg]

---

##  The Traveling Salesman Problem (TSP) 

Given a set of $n$ cities, find the minimum length tour in which each city is visited exactly once, then you return home.

* This is equivalent to finding the minimum Hamiltonian cycle.
* If distances between each of the $n$ cities are known, there are $(n - 1)!$ possible routes.
    * Eliminating reverse routes, you get $\frac{(n - 1)!}{2}$
    * Clever algorithms can do it in a polynomial factor of $2^n$  steps...
