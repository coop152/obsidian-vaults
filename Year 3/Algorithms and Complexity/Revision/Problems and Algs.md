# st-CON
Given: a directed graph $G$, and two vertices $s, t$ in that graph
Return: Yes if $t$ is reachable from $s$ in $G$, No otherwise.

In other words, decide if there is a path from s to t.
## Algorithm
- Depth First Search
# Strong Connectivity
Given: a directed graph $G$
Return: Yes if every node in the graph is reachable from every other, No otherwise.

## Algorithms
- Solve st-CON with a DFS for every pair of vertices
	- Very slow and shit, runs in quadratic time
- [Kosaraju's Algorithm](Strongly%20Connected%20Components.md#Kosaraju's%20Algorithm)
	- Find the SCCs of the graph. If there is more than 1, it is not strongly connected.
	- Runs in linear time