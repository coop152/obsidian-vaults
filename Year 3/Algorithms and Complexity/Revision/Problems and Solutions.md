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
	- `find(e)` is linear. 