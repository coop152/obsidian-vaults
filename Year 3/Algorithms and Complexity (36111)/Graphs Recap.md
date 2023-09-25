## Definitions
- **Degree** - Of a vertex is the number of *incident* edges; that is, the number of edges that are connected to it both inbound and outbound.
- **Out-degree** - Of a vertex is the number of outbound edges.
- **In-degree** - Of a vertex is the number of inbound edges.
- **Simple graph** - A graph with no *loops* (edges connecting a vertex to itself) or *parallel edges* (multiple identical edges). A simple graph has a **set** of nodes, while a non-simple graph has a **collection** of nodes.
- **Cycle** - A path that starts and ends at the same vertex.
- **Simple Cycle** - A path where each vertex is distinct apart from the start/end point. 
- **Subgraph** - A graph whose vertices and edges are subsets of the vertices and edges of another graph. (These notes will use the word "supergraph" as the inverse, but the textbook does not.)
- **Spanning Subgraph** - A subgraph whose set of vertices is the same as its supergraph. That is, a subgraph that contains all vertices of its supergraph.

## Theorems
![](Pasted%20image%2020230925150703.png)
Simple: The sum of degrees in a graph is double the number of edges, because each edge contributes +1 to the degrees of both of its endpoints.

![](Pasted%20image%2020230925150804.png)
Simple: The sum of in-degrees is equal to the sum of out-degrees in a directed graph. This is because each edge contributes +1 to the in-degree of its start point and +1 to the out-degree of its end point.

![](Pasted%20image%2020230925151514.png)
Simple: Because the graph is simple, there can be no loops. Therefore, the highest degree possible in G is $n-1$; A vertex that connects to every other. If every vertex has this highest degree (i.e. every vertex is connected to every other) then the total degree will be $n(n-1)$. Theorem 13.6 says the total degree is less than $2m$, therefore $2m \leq n(n-1)$ and $m \leq n(n-1)/2$. The same logic applies to a directed graph, but the total degree is doubled because each vertex connecting to every other requires two edges back and forth.

