A union-find structure is a data structure that supports a collection of disjoint sets. They expose these methods:
`makeSet(e)`: Given an element `e`, create a new singleton set containing `e` and name the set `e`.
`union(A, B)`: Given two sets `A` and `B` in the structure, create `a U b` and name the result either `A` or `B`.
`find(e)`: Given an element `e`, find the name of the set containing `e`.

## Union-find and Connected Components
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
The complexity of this algorithm depends on the implementation of the union-find structure. Where $n$ is the size of $S$ and $m$ is the size of $E$, we make $n$ sets, perform $m$ unions and find $m$ items. 