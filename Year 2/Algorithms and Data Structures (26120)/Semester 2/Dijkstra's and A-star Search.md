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
In plain English:
1. Start with an empty set of found nodes, and a priority queue holding the first node. The priority queue is ordered based on lowest distance first.
2. While the priority queue is not empty (i.e. there are still nodes to explore)
	1. Dequeue a node (this node being the one that has the lowest distance from the source)
	2. Add to the set of found nodes
	3. Add all of it's successors that are not found or in the queue to the queue
	4. Try to relax the graph with the dequeued node.

Relaxing:
1. For every one of the given node's successors, does that node give a shorter path than the current best?
2. If so, update the shortest distance and the predecessor map

This uses a priority queue, which complicates the implementation:
- After relaxation a node's value may **decrease**.
- We need a "decrease key" operation
- If we use a **MinHeap**, we can restore heap-property after pop, insertion or relaxation.

Because we are adding nodes as they are discovered:
- We won't explore unreachable nodes
- There is no need for infinity in the queue

When the algorithm is complete, the answers are in `F`. Take the predecessors of the desired destination to backtrack and find the shortest path.
Note that Dijkstra's does not work on graphs with **any** negative edges. That includes graphs with negative edges but no loops.
#### Complexity
Operations:
- Every edge is relaxed **at most one time**.
- Every node is added and removed from the priority queue **at most once**.

The cost of relaxing, addition and extraction in the min heap priority queue is $O(\log|V|)$.
Therefore, the complexity of Dijkstra's algorithm is $O((|E| + |V|)log|V|)$.
Better than Bellman Ford, but still pretty complex; Had to explore every node.

#### Correctness
Same loop invariant argument as with Bellman Ford.

## A* Search Algorithm
```python
procedure astar(s):
	F = empty set
	D: priorityQueue = initial estimate for s
	while D is not empty:
		u = D.pop()
		if i == goal:
			return D[u]
		F.add(u)
		for each v in u.successors where v not in F union D:
			D.add(v, heuristic(v))
		relax(u)

procedure relax(u):
	for each v in u.successors:
		if D[u] + w(u, v) < D[v]:
			D[v] = D[u] + w(u, v)
			P[v] = u
```
A* also cannot handle negative edges.
#### Heuristic Admissibility and Monotonicity
To use a heuristic it must be:
- Admissible: It must not overestimate the true cost of reaching the destination (i.e. $h(u) \leq \delta(u, t)$)
- Monotone: $h(a) \leq w(a, b) + h(b)$
