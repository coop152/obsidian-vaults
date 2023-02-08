Problem: Find the shortest path from some node to any other node in a graph, shortest meaning "the minimal number of edges".
BFS visits nodes in order of their distance from the starting node $s$, so could that be useful?
To solve the problem, introduce a *predecessor map*:
- For each node, store the node from which it was discovered
- Follow these nodes backwards until $s$ is reached
- By convention, $s$ is its own predecessor
## BFS Method
![](Pasted%20image%2020230208123910.png)
In non-maths-speak:
```python
def ShortestPaths(s):
	d = [s]
	found = {}
	predecessor = Map()
	predecessor[s] = s
	while d is not empty:
		u = d.dequeue()
		F.add(u)
		for all v that u leads to AND v not in found:
			D.enqueue(v)
			predecessor[v] = u
	return predecessor
```
Then to get the shortest path from the predecessor map:
![](Pasted%20image%2020230208124617.png)
i.e.
```python
def GetPath(pred, u):
	p = [u]  # list representing a path, start as idx 0 and end as idx n
	while pred[u] != u:  # while the starting node hasnt been reached
		u = pred[u]
		p = u + p
	return p
```

## Shortest Paths in Weighted Graphs
Similar problem, but concerned with the total weight along the path as opposed to the number of nodes.

## Representing a Weighted Graph
![](Pasted%20image%2020230208125159.png)
Similar to an adjacency matrix, with weights instead of boolean true/false values. If two nodes are not connected, the weight is said to be $\infty$.
![](Pasted%20image%2020230208125344.png)
The weight of a path is simply the sum of the weights of all of the edges it takes.
If the weight is $\infty$, there was a non-existent edge in the path, and it isn't feasible.
![](Pasted%20image%2020230208125500.png)
$\delta(u,v)$ is the shortest distance from node $u$ to node $v$. If no path exists, $\delta(u, v) = \infty$.

## Bellman Ford Algorithm
An algorithm to compute the shortest paths from some starting node $s$ to any other node.
In the context of this algorithm, shorten $\delta(s, v)$ to $\delta(v)$. Its always the same starting node anyway.
![](Pasted%20image%2020230208130140.png)
![](Pasted%20image%2020230208130048.png)
1. Create estimate map, with the distance being $\infty$ for all edges but the one connecting the start node to itself, which is 0.
2. 