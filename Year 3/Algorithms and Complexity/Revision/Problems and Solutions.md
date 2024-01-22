# st-CON
Given: a directed graph $G$, and two vertices $s, t$ in that graph
Return: Yes if $t$ is reachable from $s$ in $G$, No otherwise.

In other words, decide if there is a path from s to t.
### Algorithm
- Depth First Search
	- Runs in linear time
# Cyclicity
Given: a directed graph $G$
Return: Yes if $G$ is cyclic, no otherwise
### Algorithms
- Topological Sorting
	- Runs in linear time
# Strong Connectivity
Given: a directed graph $G$
Return: Yes if every node in the graph is reachable from every other, No otherwise.
### Algorithms
- Solve st-CON with a DFS for every pair of vertices
	- Very slow and shit, runs in quadratic time
- [Kosaraju's Algorithm](Strongly%20Connected%20Components.md#Kosaraju's%20Algorithm)
	- Find the SCCs of the graph. If there is more than 1, it is not strongly connected.
	- Runs in linear time

# Union-find (data structure)
A structure that facilitates algorithms on disjoint sets.
### Implementations
- List-based
	- Each set is represented as a modified linked list, where every element of each list has a direct pointer to the head. These lists are themselves stored in some collection in the overall union-find data structure.
	- `find(e)` is O(1). Just follow the pointer back to the head of the set.
	- `makeSet(e)` is O(1). Just make a new linked list with `e` in it and add it to the union-find structure's collection.
	- `union(a, b)` is essentially linear ($O(\min(|a|, |b|))$ which is $O(n)$ in the worst case.) Take the biggest set; this one keeps it's head and items. Add every item from the smaller set into the bigger set, then delete the smaller set and remove from the union-find structure's collection. **If you amortize over $n$ initial elements then the complexity is actually $O(\log{n})$.**
- Tree-based
	- 