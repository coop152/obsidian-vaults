## Decision Problem: st-CON
Given: a directed graph $G$, and two vertices $s, t$ in that graph
Return: Yes if $t$ is reachable from $s$ in $G$, No otherwise.

In other words, decide if there is a path from s to t.
The simplest solution is a DFS:
```python
function DFS(G, v):
	mark v as visited
	for each w in G.successors(v):
		if w is unvisited:
			DFS(G, w)
```


## Decision Problem: STRONG CONNECTIVITY
Given: a directed graph $G$
Return: Yes if every node in the graph is reachable from every other, No otherwise.

Essentially, st-CON must be true for all pairs of vertices.
This can be solved in the "obvious" way by running st-CON on every combination of vertices in the graph. This runs in quadratic time, and is an awful solution.

A **Strongly Connected Component** is a maximal subset of vertices in a graph which are strongly connected among themselves. That is, every vertex in the component is reachable from every other, and the subset is as big as it can be. You can compute the strongly connected components using Kosaraju's Algorithm.
## Kosaraju's Algorithm
You can find the SCCs of a graph in linear time. The typical way of doing this is using **Tarjan's algorithm**. However, Kosaraju's Algorithm is nicer.

It is a two-pass algorithm vaguely structured like this:
```python
algorithm Kosaraju(Graph G):
	S = new Stack()  # empty stack
	K1(G)  # first pass, populates the stack
	CC = new Set()  # set of strongly connected components
	H = G.edgeReversal()
	# implicit here; unmark every vertex in the graph
	K2(H, S)  # Second pass, using the stack, populates CC
	return CC
```
Essentially, `K1` and `K2` are depth first searches with small modifications.
```python
algorithm K1(Graph G):
	while G.hasUnmarkedVertices():
		Vertex v = G.pickUnmarkedVertex()
		dfs1(G, v)

# regular dfs but push vertex onto stack to record return order
algorithm dfs1(Graph G, Vertex v):
	v.markVisited()
	for u in v.children:
		if u.isUnmarked():
			dfs1(G, u)
	S.push(v)  # stack defined in the outermost Kosaraju() function
```
These fill the stack with every vertex in the order they were returned.
```python
# 
algorithm K2(Graph H, Stack S):
	while not S.empty():
		Vertex v = S.pop()
		if v is unmarked:
			C = new Set()
			dfs2(H, v)
			CC = CC U {C}

algorithm dfs2(Graph H, Vertex w):
	w.markVisited()
	C = C U {w}
	for u in w.children():
		if u.isUnmarked():
			dfs2(H, u)
```

### Correctness
Why does this work?
After K1 is complete, the stack (essentially) contains the DFS trees of every connected component in the graph. At the top of the stack will be the root of one of them, with all it's children below it in levels descending from the top. After each tree will be either the next tree, or the end of the stack.