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
# note that E is full initially; that is, it includes every possible wall and completely isolates every cell in G
algorithm MazeGenerator(Cell[][] G, Set<Wall> E):
	Set[Wall] R = new Set<Wall> # empty set of Walls
	while R.size() < G.cellCount() - 1:
		Edge (x, y) = E.getRandomUnchosenEdge()
		# Remove random edge, as long as it adds a new cell to the connected component
		if find(x) != find(y):
			union(find(x), find(y))
			R.add((x, y))
	return R
	
```
This algorithm works based on two facts:
- We only remove walls if they introduce a new cell into the connected component, so there will be no cells with two entrances and thus no alternative way to reach any given point
- We repeat until the number of removed walls is equal to the number of cells minus 1. Because each wall we remove adds one cell to the size of the connected component, the resulting component will include every single cell in the grid. 

Because we have a connected component, it is possible to access any cell from any other. Therefore our maze absolutely has a solution, but it also features lots of twists, turns and dead ends because every cell is reachable.
The running time of this algorithm scales linearly with the time it takes to perform $m$ union and find operations on a set of $n$ singleton sets. (The notes say $O(t(n, m))$ which is completely meaningless because $t$ is never defined but WHATEVER)

This problem actually relates to the science of **percolation theory**, the study of how liquids permeate porous materials.

## Implementation
### List-based
This is a simple implementation involving linked lists.
The base structure holds a collection of linked lists, one for each set. The list for each set contains a `head` node which stores
- the size of the set
- the name of the set
- A pointer to the first and last nodes of the actual linked list, which holds pointers to the elements of the set.

Each node of the actual linked list stores a pointer to the element that belongs to the set, as well as a pointer to the head node of the set.
![](Pasted%20image%2020230929122800.png)
With this implementation, we have these complexities:
- `find(e)`: $O(1)$. Follow the pointer from the node to the head, and return the name.
- `makeSet(e)`: $O(1)$. Create a new head node and a single element linked list containing `e`, then register this in the structure's collection of lists.
- `union(A, B)`: $O(\min(|a|, |b|))$, which is $O(n)$ in the worst case. Select the biggest list of $A$ and $B$; this biggest set keeps it's head pointer and list. Merge the items of the smaller set into the bigger set's linked list, updating the head pointer in each to point to the larger set's head.

The complexity of union sounds bad, but it actually isn't as bad as it sounds when you perform amortised analysis.

Here are the algorithm implementations:
```python
algorithm makeSet(e):
	Head u = new Head()
	u.name = "e"
	u.items.append(e)
	e.head = u

algorithm find(Item x):
	return x.head

algorithm union(Head u, Head v):
	if u.length < v.length:
		for Item x in u.items:
			v.items.add(x)
			x.head = v
		Remove u from the union-find structure
	else:
		for Item x in v.items:
			u.items.add(x)
			x.head = u
		Remove v from the union-find structure
```

There was some proof about amortised analysis that I just didn't understand, see page 227 for that.

Conclusion, if you amortise over all operations involving $n$ initial elements, union is $O(\log{n})$. makeSet and find are still $O(1)$.

## Tree-based
This is a more complicated (but more efficient) implementation based on trees.
The tree $T$ is populated by a series of nodes, each node holding a single element of the set that $T$ represents and a pointer to its parent. If the node is a root (see 1, 2 and 5 in the following diagram) then it's parent pointer points to itself; this is how a root node is identified.
Each set in the structure is identified by the root of it's tree.
![](Pasted%20image%2020231003112232.png)
With this representation, we have these complexities:
- `makeSet(e)`: $O(1)$. Make a new node that is it's own parent.
- `union(u, v)`: $O(1)$. Take the root of one tree and set it's parent to the root of the other.
- `find(e)`: $O(n)$. Traverse the tree from the given element to the root. Worst case, every element is in one long chain of length $n$.

This simple version seems on-par with the simpler list implementation, but there are optimisations that can be applied to make it better:
- **Union-by-Size**: When performing a union, make the smaller tree the child of the larger tree. This requires storing a tree size at each node. 
- **Path Compression**: When performing a find, change the parent of every node you visit to point to it's root.
![](Pasted%20image%2020231003114220.png)
Both of these optimisations increase the running time of operations by some constant amount, but they significantly improve the amortised running time of a sequence of unions and finds.
Here are the implementations of the operations:
```python
algorithm makeSet(e):
	Node x = new Node()
	x.parent = x
	x.size = 1
	return x

# Note that x and y must both be roots
algorithm union(x, y):
	if x.size < y.size:
		x.parent = y
		y.size += y.size + x.size
	else:
		y.parent = x
		x.size = x.size + y.size

algorithm find(x):
	Node r = x
	while r.parent != r:  # find the root
		r = r.parent
	Node z = x
	while z.parent != z: # go through again and perform path compression
		w = z
		z = z.parent
		w.parent = r
```

Assertion: Performing a sequence of $m$ union and find operations on this implementation, starting with $n$ singleton sets, will take $O(n+m\log{n})$ time.
Proof: There are at most $O(n)$ union operations, each running in constant time, so the time for the union operations is $O(n)$. We only change a node's parent when we union it's set into a set that is at least as large as it; therefore, the parent of each node must be at least double the size. Therefore, you can only go up a max of $O(\log{n})$ nodes before you have reached the size of the entire set by doubling.

### Detailed Analysis
We can do a more detailed analysis using the **Ackermann function**, which is defined as such:
![](Pasted%20image%2020231003120809.png)
![](Pasted%20image%2020231003121715.png)
We use $A(x) = A_x(2)$, which is extremely fast-growing. Likewise, the inverse $\alpha(x)$ is extremely slow-growing.

More definitions before the analysis really starts:
$U$ - The tree given after performing every `union` operation, but without performing any path compressions (i.e. `find`s)
$n(v)$: For node $v$, $n(v)$ is the number of nodes in $v$'s subtree
$r(v)$: The rank of node $v$: $r(v) = \lfloor\log{n(v)}\rfloor + 2$

We can immediately rearrange the definition of rank to get $n(v) \geq 2^{r(v)-2}$ (greater than because of the removed floor.) Because there are at most $n$ nodes in $U$, you can also say that $r(v) \leq \lfloor\log{n}\rfloor + 2$ (because the largest possible $n(v)$ is $n$).

Take these as fact, because I do not understand the proofs:
If node $w$ is the parent of node $v$ then $$r(v) < r(w).$$
That is, ranks are strictly increasing as you go up the tree.
The number of nodes of some rank $s$ is at most $$\frac{n}{2^{s-2}}.$$
This entire section is just nonsense, actually.

Final result: The total time to perform $m$ union and find operations, with the optimisations given, is $O((n + m)\alpha(n))$. $\alpha$ being a very slow-growing function. 