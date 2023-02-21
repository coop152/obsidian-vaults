For some graph, the MST is a subgraph that connects every node with the minimum total summative weight possible. MSTs are always acyclic by definition; they are trees.
As a formal problem:
![](Pasted%20image%2020230221121459.png)
## Generic Strategy
A generic greedy strategy, which won't always be optimal.
```python
procedure genericMST(G, w):
	A = {}
	while A is not a spanning tree:
		E = find an edge that is safe for A
		A = A union {(u, v)}
	return A
```
## Cuts & Light Edges
If you have a graph $G=(V, E)$, then a cut $(S, V-S)$ of that graph is a partition of $V$.
A cut respects a set of edges $A$ if $A$ contains no edges which **cross** the cut. An edge **crosses** a cut if one of its endpoints is in $S$ and the other in $V - S$.
If you have a set of edges crossing a cut, the edge with minimal weight is called the **light edge**. (You can have many light edges.)
![](Pasted%20image%2020230221122652.png)
In this example, the light edge is the one between Bonn and Karlsruhe.
## Kruskal's Algorithm
An algorithm to find an MST of a graph.
```python
procedure kruskal(G, w):
	A = {}
	D = empty disjoint set
	for each node v in G:
		D.make_set(v)  # add set {v} to the disjoint set structure
	sort edges in G by weight w ascending
	for each edge (u, v) in G:  # starting with the smallest weighted edge
		if D.find_root(u) != D.find_root(v):  # if the nodes are disconnected
			A = A.add((u, v))  # add edge to MST, connecting the nodes
			D.union(u, v)  # join the two sets in the disjoint set structure
```