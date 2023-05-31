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
	A = {}  # set of edges in the MST
	D = empty disjoint set
	for each node v in G:
		D.make_set(v)  # add set {v} to the disjoint set structure
	sort edges in G by weight w ascending
	for each edge (u, v) in G:  # starting with the smallest weighted edge
		if D.find_root(u) != D.find_root(v):  # if the nodes are disconnected
			A = A.add((u, v))  # add edge to MST, connecting the nodes
			D.union(u, v)  # join the two sets in the disjoint set structure
	return A
```
In plain English (and disregarding the concrete implementation using a disjoint set):
1. Make an empty set that will hold edges. This represents the resulting MST.
2. Sort the edges in the graph in ascending order of weight
3. For each edge, starting with the one of least weight, consider if you can add that edge to the MST without introducing a cycle. If you can, add it to the set.
4. When you have considered every edge, the algorithm is complete. Return the set representing the MST.

For a worked example, watch **the end of the second video in the MST week**.
#### Complexity
Depends on the implementation of the disjoint-set data structure. As long as path-compression and union-by-rank heuristics are used, it will be $O(|E|log|V|)$.
## Prim's Algorithm
Another algorithm for finding an MST.
```python
procedure prim(G, w, r):  # start from some arbitrary node r
	V = G.nodes
	for each node u in V:
		u.key = infinity
		u.parent = None
	r.key = 0
	Q = priority_queue(V)  # lower key has higher priority
	while Q is not empty:  # for all unconnected nodes
		u = Q.dequeue()  # take the node with the lowest incoming weight
		for each node v where w[u, v] != infinity:  # for each edge out of u
			# if this edge weighs less than the existing connection
			if Q.contains(v) and w[u, v] < v.key:   
				v.parent = u
				v.key = w[u, v]
```
In plain english:
1. 
#### Complexity
Depends on the implementation of the priority queue. If a fibonacci heap is used, it will be $O(|E| + |V|log|V|)$.