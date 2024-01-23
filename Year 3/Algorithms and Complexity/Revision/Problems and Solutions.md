# st-CON
Given: a directed graph $G$, and two vertices $s, t$ in that graph
Return: Yes if $t$ is reachable from $s$ in $G$, No otherwise.

In other words, decide if there is a path from s to t.
### Algorithm
- Depth First Search
	- Runs in linear time
---
# Cyclicity
Given: a directed graph $G$
Return: Yes if $G$ is cyclic, no otherwise
### Algorithms
- Topological Sorting
	- Runs in linear time
---
# Strong Connectivity
Given: a directed graph $G$
Return: Yes if every node in the graph is reachable from every other, No otherwise.
### Algorithms
- Solve st-CON with a DFS for every pair of vertices
	- Very slow and shit, runs in quadratic time
- [Kosaraju's Algorithm](Strongly%20Connected%20Components.md#Kosaraju's%20Algorithm)
	- Find the SCCs of the graph. If there is more than 1, it is not strongly connected.
	- Runs in linear time
---
# Union-find (data structure)
A structure that facilitates algorithms on disjoint sets.
### Implementations
- List-based
	- Each set is represented as a modified linked list, where every element of each list has a direct pointer to the head. These lists are themselves stored in some collection in the overall union-find data structure.
	- `find(e)` is O(1). Just follow the pointer back to the head of the set.
	- `makeSet(e)` is O(1). Just make a new linked list with `e` in it and add it to the union-find structure's collection.
	- `union(a, b)` is essentially linear ($O(\min(|a|, |b|))$ which is $O(n)$ in the worst case.) Take the biggest set; this one keeps it's head and items. Add every item from the smaller set into the bigger set, then delete the smaller set and remove from the union-find structure's collection. **If you amortize over $n$ initial elements then the complexity is actually $O(\log{n})$.**
- Tree-based
	- The entire structure is represented as a tree. A set is identified by the root of its tree (the root being denoted by being its own parent.)
	- `find(e)` is O(n). Traverse the tree from the given element to the root by repeatedly taking the parent. In the worst case, the tree is just one big chain and there are $n$ elements.
	- `makeSet(e)` is O(1). Just make a new root node.
	- `union(a, b)` is O(1). Just make the root of one tree the parent of the root of the other tree.
	- `find` can be made faster with these optimisations:
		- **Union-by-size**: when performing a union, always make the smaller tree the child. This requires storing the current tree size in each node.
		- **Path compression**: when performing a find, set the parent of every node you traverse directly to the root.
---
# String Matching
Given: a text string $T$ and a pattern string $P$
Return: The index of the substring in $T$ which equals $P$, or No Match if $T$ doesn't contain $P$
### Algorithms
- Naïve algorithm
	- Just compare the pattern to the text at every single index.
	- Complexity is bad at $O(nm)$ (where n = length of $T$ and m = length of $P$). If the pattern is at least half as long as the text string then it becomes $O(n^2)$.
- Rabin-Karp algorithm
	- 
- Knuth-Morris-Pratt (KMP) algorithm
	- 
---
# Maximum Flow Problem
Given: a flow network $F$ specified by weighted digraph $D$, source node $s$ and target/sink node $t$
Return: Maximum flow through $F$

### Algorithms
- Ford-Fulkerson Algorithm
	- Find augmenting paths in the network (paths with a non-zero residual capacity) and push flow across them to increase the total flow of the network. Do this until no more augmenting paths remain, and the maximum flow has been reached.
	- Pseudo-polynomial-time complexity. Can be as bad as $O(|f^*|m)$ in the worst case (where $f^*$ is the maximum flow).
- Edmonds-Karp
	- Variant of Ford-Fulkerson where paths with the least number of edges are chosen, in order to prevent the worst case scenario in which Ford-Fulkerson's complexity suffers.
	- Polynomial time complexity of $O(nm^2)$, where $n$ is the number of vertices and $m$ is the number of edges.
---
# Maximum Bipartite Matching
Given: bipartite graph $G$
Return: Maximum matching in $G$
### Algorithms
- Reducible to the maximum flow problem, to then be solved with an algorithm for that problem
	- Make a new flow network $F$. Add all of the vertices from $G$ to $F$, as well as new sink and target nodes. Add edges from the sink to every node in one of the sets (call this set $X$). Now include the edges from $G$ in $F$, directed from $X$ to the other set (call this set $Y$). Now add edges from all vertices in $Y$ to the target. Every edge should have a capacity of 1.
	- Finding a maximum flow for this network yields a maximum matching; interpret every edge in the flow with 1 flow as a match, and every edge with 0 flow as non-existent. The complexity of this is the same as whichever algorithm is used to find the maximum flow (i.e. probably $O(nm^2)$.)
- Gale-Shapely Algorithm
	- (In the marriage analogy:) Also takes ratings from both men and women on the members of the other set. The result is not just a maximum matching, but is also a matching such that no woman would rather be matched with an unmatched man than the one she has already been matched with.
---
# Minimum-cost Flow
Given: a flow network $F$ specified by weighted digraph $D$, source node $s$ and target/sink node $t$, with costs on each edge of $w(e)$.
Return: Maximum flow through $F$ with the minimum cost
