

## Decision Problem: st-CON
Given: a directed graph $G$, and two vertices $s, t$ in that graph
Return: Yes if $t$ is reachable from $s$ in $G$, No if it is not.

In other words, decide if there is a path from s to t.
The simplest solution is a DFS:
```python
function DFS(G, v):
	mark v as visited
	for each w in G.successors(v):
		if w is unvisited:
			DFS(G, w)
```
