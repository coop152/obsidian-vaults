Bellman Ford, our previous best algorithm for finding shortest paths, is $O(|V||E|)$ in complexity, which is poor. 
## Dijkstra's Algorithm
```python
procedure dijkstra(s):
	F = empty set
	D: priorityQueue = initial estimate for s
	while D is not empty:
		u = D.pop()
		F.add(u)
		for each v in u.successors where v not in F union D:
			D.add(v)
		relax(u)

procedure relax(u):
	for each v in u.successors:
		if D[u] + w(u, v) < D[v]:
			D[v] = D[u] + w(u, v)
			P[v] = u
```