A union-find structure is a data structure that supports a collection of disjoint sets. They expose these methods:
`makeSet(e)`: Given an element `e`, create a new singleton set containing `e` and name the set `e`.
`union(A, B)`: Given two sets `A` and `B` in the structure, create `a U b` and name the result either `A` or `B`.
`find(e)`: Given an element `e`, find the name of the set containing `e`.

## Applications
### Connected Components
Suppose we are given a network, in this instance a social network of friends. This network $N$ is defined by a set $S$ of people and a set $E$ of edges representing relationships between people.
We want to find all of the connected components of $N$ - in this example, that is all of the "social circles" of people who are friends, friends of friends, etc.
![](Pasted%20image%2020230929112304.png)
Here is pseudocode for solving this problem using a union-find structure:
```python
algorithm UFConnectedComponents(Set<Node> S, Set<Edge> E):
	for node x in S:
		makeSet(x)  # make singleton set for each node
	for each edge (x, y) in E do:
		# union sets based on given edges
		if find(x) != find(y):
			union(find(x), find(y))
	for node x in S:
		print("Person {x} belongs to connected component" find(x))
```
The complexity of this algorithm depends on the implementation of the union-find structure. Where $n$ is the size of $S$ and $m$ is the size of $E$, we make $n$ sets, perform $m$ unions and find $m$ items. If we use a list-based implementation of the structure we can perform the algorithm in $O((n+m)\log{n})$ time, and if we use a tree-based structure we can do it in "almost" $O(n+m)$ (but not quite.)
Note that if we are given the set of edges $E$ in a sorted order (for example, in lexicographic order) then we can design an algorithm that runs in actual $O(n+m)$ time.

### Maze Construction & Percolation Theory
Another (less important sounding) use for union-find structures is for constructing mazes. The definition of a maze here is a 2D grid made up of cells which can be traversed and walls which cannot.
![](Pasted%20image%2020230929114050.png)
The goal is to create a maze with a single solution, and with a solution that isn't trivial (e.g. a straight line down and then right from start to finish).
Here is an algorithm for creating such a maze:
```python
algorithm MazeGenerator(Grid G, Set[Wall] E):
	
```