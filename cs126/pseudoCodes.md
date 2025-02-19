--- 
layout: CS126
part: true
math: true
title: "Graph Related Pseudocode"
header-includes:
    - \usepackage{algorithm2e}
---


# Depth First Search (DFS)
## DFS for a particular node: <br>

The following algorithm is pseudocode for Depth First Search - as displayed by the CS126 lectures

**Algorithm** $DFS(G, v)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp; **Input**  graph $G$ and start at vertex $v$ of $G$<br>
&nbsp;&nbsp;&nbsp;&nbsp; **Output** labeling of the edges of $G$ in the connected component of v as discovery edges and back edges 
<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; 
$setLabel(v, VISITED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp; 
**for all** $e \in G.incidentEdges(v)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 
**if** $getLabel(e) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$w \leftarrow opposite(v,e)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(w) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e, DISCOVERY)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$DFS(G, w)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**else** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e,BACK)$ <br>

**END ALGORITHM**

<br>
<br><br>

## DFS for an entire graph: 
The following algorithm is pseudocode for Depth First Search - as displayed by the CS126 lectures, which is used to perform depth first search on the entire graph.
  
**Algorithm** $DFS(G)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp; **Input**  graph $G$ <br>
&nbsp;&nbsp;&nbsp;&nbsp; **Output** labelling of the edges of $G$ as discovery and back edges
<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; **for all** $u \in G.vertices()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **$setLabel(u, UNEXPLORED)$** <br>

&nbsp;&nbsp;&nbsp;&nbsp; **for all** $e \in G.edges()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **$setLabel(e, UNEXPLORED)$** <br>

&nbsp;&nbsp;&nbsp;&nbsp; **for all** $u \in G.vertices()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **if $getLabel(u, UNEXPLORED)$** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; $DFS(G, v)$ <br>

**END ALGORITHM**
<br>

**Algorithm** $DFS(G, v)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp; **Input**  graph $G$ and start at vertex $v$ of $G$<br>
&nbsp;&nbsp;&nbsp;&nbsp; **Output** labeling of the edges of $G$ in the connected component of v as discovery edges and back edges 
<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; 
$setLabel(v, VISITED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp; 
**for all** $e \in G.incidentEdges(v)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 
**if** $getLabel(e) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$w \leftarrow opposite(v,e)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(w) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e, DISCOVERY)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$DFS(G, w)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**else** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e,BACK)$ <br>

**END ALGORITHM**
<br><br><br>

## Path Finding with DFS
By using an alteration of the depth first search algorithm, we can use it to find a path between two given vertices, using the **template method pattern**

**Algorithm**
$pathDFS(G,v,z)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(v, VISITED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$S.push(v)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;
**if** $v=z$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**return** $S.elements()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $e \in G.incidentEdges(v)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(e) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$w \leftarrow opposite(v,e)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(w) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e,DISCORVERY)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$S.push(e)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$pathDFS(G,w,z)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$S.pop(e)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**else**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e, BACK)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$S.pop(v)$<br>

**END ALGORITHM**

<br><br><br>

## Cycle Finding with DFS

**Algorithm** $cycleDFS(G,v)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(v,VISITED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$S.push(v)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $e \in G.incidentEdges(v)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(e) = UNEXPLORED$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$w \leftarrow opposite(v,e)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$S.push(e)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(w)= UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $setLabel(e,DISCOVERY)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$cycleDFS(G,w)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$S.pop(e)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**else** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**T** $\leftarrow$ new empty stack <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**repeat**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$o \leftarrow S.pop()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$T.push(o)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**until** $o=w$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**return** $T.elements()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$S.pop(v)$<br>

**END ALGORITHM**

<br><br><br>

# Breadth First Search (BFS) Pseudocode
This algorithm is a graph traversal algorithm which follows the general directive: <br>

1. First visit all vertices that are a distance of 1 from then starting node.
2. Second, visit all vertices of distance 2, up until all vertices have been visited.

The result of this, is that each time an edge is reached, it is one (possibly of many) of the shortest paths to that particular node. 



**Algorithm** $BFS(G)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp; 
**Input** graph $G$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**Output** labeling of the edges and partition of the vertices of G<br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $e \in G.vertices()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(u, UNEXPLORED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $e \in G.edges()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e, UNEXPLORED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $v \in G.vertices()$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(v) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$BFS(G,v)$
<br>

**END ALGORITHM**
<br><br><br>
**Algorithm** $BFS(G, s)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$L_0 \leftarrow$ new empty sequence <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$L_0 .addLast(s)$  <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(s, VISITED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$i \leftarrow 0$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**while** $¬L_i .isEmpty()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$L_i+1 \leftarrow$ new empty sequence <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $v\in L_i .elements()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $e \in G.incidentEdges(v)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(e) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$w \leftarrow opposite(v,e)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(w) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e) = (e, DISCOVERY)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(w,VISITED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$L_i+1 .addLast(w)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**else** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(e,CROSS)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$i \leftarrow i + 1$
<br>

**END ALGORITHM**


## Directed graphs

- This algorithm is the Floyd Warshall algorithm, which is used to compute the transitive closure on a directed graph.

**Algorithm** $FloydWarshall(G)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**Input** digraph $G$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**Output** transitive closure $G^*$ of $G$ <br><br>
&nbsp;&nbsp;&nbsp;&nbsp;
$i \leftarrow 1$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $v \in G.vertices()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
denote $v$ as $v_i$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$i \leftarrow i + 1$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
$G_0 \leftarrow G$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for** $k \leftarrow 1$ **to** $n$ **do**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$G_k \leftarrow G_{k-1}$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**for** $i\leftarrow 1$ **to** $n(i\neq k)$ **do** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**for** $j \leftarrow 1$ **to** $n(j\neq i, k)$ **do** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $G_{k-1}.areAdjacent(v_i,v_k)$  $\&$ $G_{k-1}.areAdjacent(v_k,v_j)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $¬G_{k-1}.areAdjacent(v_i,v_j)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$G_k.insertDirectedEdge(v_i,v_j,k)$<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**return** $G_n$
<br><br><br>

### Topological ordering using DFS 

**Algorithm** $topologicalDFS(G)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**Input** dag $G$<br>
&nbsp;&nbsp;&nbsp;&nbsp;
**Output** topotlogical ordering of G <br><br>
&nbsp;&nbsp;&nbsp;&nbsp;
$n \leftarrow G.numVertices()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $u\in G.vertices()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(,UNEXPLORED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $v\in G.vertices()$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(v) = UNEXPLORED$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$topologicalDFS(G,v)$
<br><br><br>
**Algorithm** $topologicalDFS(G,v)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**Input** graph $G$ and start a vertex $v$ of $G$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**Output** labeling of the vertices of G in the connected component of $v$ <br><br>
&nbsp;&nbsp;&nbsp;&nbsp;
$setLabel(v, VISITED)$ <br>
&nbsp;&nbsp;&nbsp;&nbsp;
**for all** $e\in G.outEdges(v)$  <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$w\in opposite(v,e)$ // Outgoing edges <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**if** $getLabel(w) = UNEXPLORED$  <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$topologicalDFS(G,w)$ // $e$ is a discovery edge<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**else** <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
Label $v$ with topological number $n$ // $e$ is a forward or cross edge<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
$n\leftarrow n - 1$ <br>

