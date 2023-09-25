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

Essentially, st-CON must be true for all vertices.
This can be solved in the "obvious" way by running st-CON on every combination of vertices in the graph. This runs in quadratic time, and is an awful solution.
A better solution is to *(lecturer was going too quick and i forgot the solution)*

