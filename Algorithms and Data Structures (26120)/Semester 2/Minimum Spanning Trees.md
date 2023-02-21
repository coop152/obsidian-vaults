For some graph, the MST is the subgraph that connects every node with the minimum total summative weight possible.
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