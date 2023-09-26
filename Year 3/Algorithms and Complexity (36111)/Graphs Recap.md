## Graphs
### Definitions
- **Degree** - Of a vertex is the number of *incident* edges; that is, the number of edges that are connected to it both inbound and outbound.
- **Out-degree** - Of a vertex is the number of outbound edges.
- **In-degree** - Of a vertex is the number of inbound edges.
- **Simple graph** - A graph with no *loops* (edges connecting a vertex to itself) or *parallel edges* (multiple identical edges). A simple graph has a **set** of nodes, while a non-simple graph has a **collection** of nodes.
- **Cycle** - A path that starts and ends at the same vertex.
- **Simple Cycle** - A path where each vertex is distinct apart from the start/end point. 
- **Subgraph** - A graph whose vertices and edges are subsets of the vertices and edges of another graph. (These notes will use the word "supergraph" as the inverse, but the textbook does not.)
- **Spanning Subgraph** - A subgraph whose set of vertices is the same as its supergraph. That is, a subgraph that contains all vertices of its supergraph.
- **Spanning Tree** - A spanning subgraph that is also a tree (i.e. has no cycles).
- **Strongly Connected** - A graph is strongly connected if every vertex is reachable from every other, directly or indirectly.
- **Connected Components** - If a graph is not **connected**, then the maximally connected subgraphs are called the Connected Components of the graph.

### Theorems
![](Pasted%20image%2020230925150703.png)
Simple: The sum of degrees in a graph is double the number of edges, because each edge contributes +1 to the degrees of both of its endpoints.

![](Pasted%20image%2020230925150804.png)
Simple: The sum of in-degrees is equal to the sum of out-degrees in a directed graph. This is because each edge contributes +1 to the in-degree of its start point and +1 to the out-degree of its end point.

![](Pasted%20image%2020230925151514.png)
Simple: Because the graph is simple, there can be no loops. Therefore, the highest degree possible in G is $n-1$; A vertex that connects to every other. If every vertex has this highest degree (i.e. every vertex is connected to every other) then the total degree will be $n(n-1)$. Theorem 13.6 says the total degree is less than $2m$, therefore $2m \leq n(n-1)$ and $m \leq n(n-1)/2$. The same logic applies to a directed graph, but the total degree is doubled because each vertex connecting to every other requires two edges back and forth.

![](Pasted%20image%2020230925190050.png)
Proof: just think about it for a few minutes

## Depth-first Search
(Looks different from previous depictions because this textbook represents edges in a graph as objects unto themselves as opposed to abstract relations between nodes)
```python
function DFS(Graph G, Node v):
	v.labelAsExplored()
	for Edge e in v.edges:
		if e.isUnexplored():  # edge has not been traversed yet
			Node w = e.destination
			if w.isUnexplored():  # found a new node via this edge
				e.markAsDiscovery()
				DFS(G, w)  # recur on newly discovered node
			else:  # found an explored node via this edge
				e.markAsBack()
```
If the graph is not connected, you need to do this in a loop for every vertex.
### Complexity
In one run of DFS, every vertex is analysed exactly once and every edge is analysed exactly twice (Note that these are only vertices and edges **in the connected component of the starting vertex.**) Therefore, DFS runs in $O(m)$ time, where $m$ is the number of edges in the starting vertex's connected component. This complexity depends on a few properties of the data structure used to represent the graph:
- Finding the incident edges to a vertex must be $O(\text{degree}(v))$. Adjacency lists satisfy this, but matrices do not.
- We must be able to mark vertices (and/or edges) as explored and to check this marking in $O(1)$ time. This can be achieved by constructing vertex/edge objects with an `explored` field, for example.

## Theorems
![](Pasted%20image%2020230926104728.png)
Simple: A run of DFS checks each vertex one time and each edge two times (at both ends.) Therefore, it runs in $O(n + m)$ time. These other algorithms exist because they can be reduced to DFS.

## Breadth-first Search
Sane version for normal people:
```python
function BFS(Graph G, node v):
	v.markAsExplored()
	Queue q = new Queue()
	for Edge e in v.edges:  # queue up all edges outbound from v
		q.enqueue(e)
	while not q.empty():  # while there remains unexplored nodes
		Edge e = q.dequeue()
		Node w = e.destination
		if w.isNotExplored():  # found a new node via this edge
			e.markAsDiscovery()
			q.enqueue(w)
		else:
			e.markAsCross() 
```
Insane version that the book gives:
```python
function BFS(Graph G, node s):
	List[List] levels = new List[List]  # every level of nodes goes in here
	levels[0] = new List  # create the 0th level
	s.markAsExplored()
	levels[0].append(s)
	int i = 0
	while levels[i] is not empty:  # repeat until we encounter an empty level
		levels[i + 1] = new List
		for Node v in levels[i]:
			for Edge e in v.edges:
				Node w = e.destination
				if e.isNotExplored():
					if w.isNotExplored():
						e.markAsDiscovery()
						w.markAsExplored()
						levels[i + 1].append(w)
					else:
						e.markAsCross()
		i++  # go to the next level
```
The levels in this version represent the length of the shortest path from start node $s$ to any given node.

### Theorems
![](Pasted%20image%2020230926111258.png)
Simple: these are obviously apparent

![](Pasted%20image%2020230926111334.png)
Simple: Same as the explanation for DFS basically

## Directed Graph Specifics
- **Reachability** - If a node is able to be reached from another node in a directed graph. Node $w$ is **reachable** from node $v$ if there is a directed path from $v$ to $w$.
- **Transitive closure** - For a directed graph $G$, the transitive closure $G^*$ is a graph containing the same vertices as $G$ but containing an edge for every directed path in $G$. That is, if $G$ contains a path from node $v$ to node $w$ then $G^*$ contains **a direct edge** connecting the nodes.

Traversing a directed graph is the same complexity as an undirected one. I'm not writing down the proof.

See [Strongly Connected Components](Strongly%20Connected%20Components.md) For more content relating to directed graph operation complexity.