Consider a computer network modelled by a digraph $G$. Each vertex represents a computer, each edge represents a one-way communication channel from one computer to another, and the weight of each edge represents the *bandwidth* of that channel (i.e. the maximum number of bytes that can be sent along that channel per second.)
Suppose we want to send a high-bandwidth stream of data from some computer $s$ to some computer $t$, even higher bandwidth than the highest bandwidth single link in the network. This might seem impossible at first, but it may be possible by splitting our stream into packets and routing these packets through multiple paths in the network.
We formulate this problem by imagining that each edge in $G$ represents a "pipe" that can transport some commodity (in this case, packets) with the weight of that edge representing the maximum amount it can transport per unit time. When formulated like this, the problem is known as the **maximum flow problem**.

# Maximum Flow Problem
We are given a weighted, directed graph $G$ and asked to find a way of transporting the maximum amount of the given commodity from some vertex $s$, called the *source*, to some vertex $t$, called the *sink*.
![](Pasted%20image%2020231009110636.png)
The maximum flow problem is closely related to the **maximum matching problem**, where you find the maximum number of ways to match vertices of one type in a graph to vertices of another type. The maximum flow problem can be used to efficiently solve the maximum matching problem, which will be shown subsequently.
Sometimes in solving the problem we may have many different maximum flows. Although they may be equal in the flow they produce (i.e. the bandwidth they provide) they may be different in cost. We will also study methods for computing the maximum flow of minimum cost.

# Flows
A flow network $N$ consists of the following:
- A connected digraph $G$ with non-negative integer weights on the edges. The weight of an edge $e$ is called the **capacity** $c(e)$ of $e$.
- Two vertices $s$ and $t$, called the **source** and **sink** respectively, such that $s$ has no incoming edges and $t$ has no outgoing edges.

![](Pasted%20image%2020231009111115.png)
We define a "flow" for the network $N$ is an assignment of some integer value $f(e)$ to each edge $e$ that satisfies the following properties:
![](Pasted%20image%2020231009111211.png)
In other words, the flow through each edge must not exceed it's capacity, and the amount of flow going into one vertex must equal the amount of flow leaving the vertex. Here is an example flow for the previously shown graph:
![](Pasted%20image%2020231009111527.png)
(For a label $x/y$, $x$ is the amount given by the flow and $y$ is the capacity of that edge.)
The total **flow** of the graph ($|f|$) is equal to the total amount of flow leaving the source $s$:
![](Pasted%20image%2020231009111752.png)
It is also clearly apparent that the flow value is equal to the total amount of flow entering the sink $t$:
![](Pasted%20image%2020231009111818.png)
A **maximum flow** for $N$ is a flow with the maximum possible value of $|f|$ of all flows for $N$. It is the most efficient possible use of a flow network. Here is an example of the maximum flow of the previous graph:
![](Pasted%20image%2020231009111957.png)
# Cuts
Flows are closely related to cuts. In the context of flow networks:
Cuts are a division of the vertices of a network $N$ into two sets, with the source on one side and the sink on the other.
Formally speaking, a **cut** of $n$ is a partition $\chi = (V_s, V_t)$ of the vertices of $N$ where $s \in V_t$ and $t \in V_t$.
An edge with an origin in $V_s$ and a destination in $V_t$ is said to be a **forward edge** of cut $\chi$.
An edge with an origin in $V_t$ and a destination in $V_s$ is said to be a **backward edge** of cut $\chi$.
Envision a cut as a separation of $s$ and $t$ done by cutting across the edges of $N$, with forward edges going from $s$'s side to $t$'s side and vice versa.
![](Pasted%20image%2020231009112727.png)
The **flow across cut $\chi$** (denoted $f(\chi)$) is equal to the sum of the flows in the forward edges of $\chi$ minus the sums of the flows in the backward edges of $\chi$. In other words, it is the net amount of commodity that flows from $s$'s side to $t$'s side. Consider this property of $f(\chi)$:
![](Pasted%20image%2020231009113143.png)
Simple: For every vertex $v$ in $s$'s side of the partition other than $s$, the incoming and outcoming flow of $v$ will be equal and cancel out in the inner equation. For $s$ itself, there are no incoming edges and the inner equation will equal $|f|$. Therefore, $F$ will equal $|f|$ as every other vertex gave 0.
You can consider it another way; every edge in $V_s$ that isn't crossing the cut will contain both terms and cancel out. Every edge that *is* crossing the cut will contain one but not both; forward edges will contain $f(e)$ and backward edges will contain $-f(e)$. Adding these up gives $F=f(\chi)$.
Therefore $F = |f| = f(\chi)$.

This shows that no matter where we cut a flow network, the flow across the cut is equal to the flow of the entire network.

We also define the **capacity** of a cut $\chi$, denoted $c(\chi)$, as the sum of the capacities of the forward edges in $\chi$ (but not the backward edges). The following lemma shows that $c(\chi)$ of a cut is an upper bound on any flow across $\chi$.
![](Pasted%20image%2020231009132630.png)
Simple:
By the definition of $f(\chi)$, we have that it equals the sum of the outgoing edges' flows minus the sum of the incoming edges' flows. We can drop the negative terms and convert the equation into an inequality, giving us that $f(\chi)$ is less than or equal to the sum of the outgoing edges' flows.
The capacity rule says that the flow of each edge is less than it's capacity, so we substitute this in to get that $f(\chi)$ is less than the sum of the outgoing edges' capacities.
The sum of the outgoing edges' capacities is the capacity of the cut, meaning that the flow of a cut is less than or equal to it's capacity.

We can combine these two lemmas to arrive at this important result:
![](Pasted%20image%2020231009133705.png)
Simple: Take any cut $\chi$ of the flow network $N$. The capacity of $\chi$ is an upper bound on every flow for $N$. This applies even to **minimum cuts** of $N$, allowing us to find the strictest upper bound on the maximum flow.

We have shown that the value of a maximum flow is no more than the capacity of a minimum cut.
We can go further, though, and prove that the value of a maximum flow is in fact **equal** to the capacity of a minimum cut.

# Residual Capacity
To prove that a certain flow is maximum, we need a way to show there is no more flow that can possibly be introduced into it. The following concepts of **residual capacity** and **augmenting paths** allow us to do this. 

Let $N$ be a flow network, specified by graph $G$, with capacity $c$, source $s$, and sink $t$.
Let $f$ be a flow for $N$. Given an edge $e$ directed from vertex $u$ to $v$, the **residual capacity** from $u$ to $v$ with respect to flow $f$ is defined as:
$$\Delta_f(u,v) = c(e) - f(e)$$
and the residual capacity in the opposite (wrong) direction is defined as:
$$\Delta_f(v, u) = f(e)$$
In other words, it is the additional capacity that has not been taken advantage of by flow $f$.

Let $\pi$ be a path from $s$ to $t$ that is allowed to traverse edges in either the forward *or* the backward (wrong) direction. In this special kind of path, a **forward edge** is defined as an edge that occurs in the path and is followed normally while a **backward edge** is an edge that occurs in the path and is followed backwards. Let's extend the definition of residual capacity from a pair of vertices to an edge:
$$\Delta_f(e)= \begin{cases}
	c(e) - f(e) & \text{if } e \text{ is a forward edge.} \\
	f(e) & \text{if } e \text{ is a backward edge.}
\end{cases}$$
In other words, the residual capacity of an edge $e$ going in the forward direction is the leftover capacity that $f$ is yet to consume, while the residual capacity in the opposite direction is the flow that $f$ has already consumed (and could potentially "give back", if there were a more optimal solution).

# Augmenting Paths
We define the residual capacity $\Delta_f(\pi)$ of a path $\pi$ as the minimal residual capacity of its edges. That is,
![](Pasted%20image%2020231009140025.png)
This represents the maximum amount of addition flow we can possibly direct down the path $pi$ without violating a capacity constraint on one of the edges. An **augmenting path** for flow $f$ is a path $\pi$ from the source $s$ to the sink $t$ with non-zero residual capacity. Specifically this means that for each edge $e$ in $\pi$:
![](Pasted%20image%2020231009140558.png)
Here is an example of an augmenting path:
![](Pasted%20image%2020231009140752.png)
(Notice that $(v_1, v_3)$ is travelled backwards.)
The residual capacity of this path is 2 (from the edge with the lowest residual capacity, $(v_1, v_4)$). We can "push" this residual capacity along the path by increasing the flow of each forward edge and decreasing the flow of each backwards edge, resulting in a new flow $f'$:
![](Pasted%20image%2020231009141150.png)
We can always add the residual capacity of an augmenting path to a flow to get another valid flow in this manner, as shown by this lemma:
![](Pasted%20image%2020231009141341.png)
Simple: Because we are adding less than the residual capacity to forward edges we will never exceed their capacities, and because we are subtracting less than the residual capacity from backward edges we will never go below zero. Therefore, the new flow is valid and the new flow is increased by $\Delta_f(\pi)$.

According to this lemma, the existence of an augmenting path $\pi$ for a flow $f$ implies that $f$ is not maximal. In addition, if we are given an augmenting path $\pi$ we can improve $f$ by pushing $\Delta_f(\pi)$ units of flow along it.
Conversely, what if there is no augmenting path? In this case, we know that $f$ is a maximal flow, which is shown by the following lemma:
![](Pasted%20image%2020231009143430.png)

These lemmas give rise to some algorithms for finding maximum flows.
# The Ford-Fulkerson Algorithm
The idea of this algorithm is to incrementally increase the value of a flow in stages by pushing along augmenting paths.
Initially the flow of each edge equals zero. At each stage, an augmenting path $\pi$ is computed and then the corresponding amount of flow is pushed along it. The algorithm terminates when the current flow $f$ does not produce an augmenting path.
Consider this pseudocode implementation:
```python
algorithm MaxFlowFordFulkerson(Network N):
	Flow f = new Flow()
	for Edge e in N:
		f(e) = 0
	Bool stop = false
	while (!stop):
		pi = N.findAugmentingPath()
		if pi.exists():  # if an augmenting path exists
			# compute the residual capacity of pi
			resCap = +Infinity
			for Edge e in pi:
				if f.residualCapacity(e) < resCap:
					resCap = f.residualCapacity(e)
			# Push the residual capacity along pi
			for Edge e in pi:
				if e.isForwardEdge():
					f(e) += resCap
				else:  # backward edge
					f(e) -= resCap
		else:
			stop = true  # maximum flow found
```

## Implementation Details
To represent a flow, we label each edge of the network with an attribute representing the flow along that edge.
To compute an augmenting path, we use a special traversal of the graph $G$ representing the flow network; such a traversal is a simple modification of DFS or BFS, where instead of considering all incident edges on the current vertex $v$, we only consider:
- The outgoing edges of $v$ with flow less than their capacity
- The incoming edges of $v$ with nonzero flow.

Alternatively, we create a new directed graph $R_f$ (the **residual graph**) from $G$ by taking the same set of vertices, and adding edges between vertices $u$, $v$ where $\Delta_f(u,v) > 0$. We can then perform a regular DFS traversal on this residual graph to find an augmenting path.
## Analysis
Let $n$ and $m$ be the number of vertices and edges of the flow network, respectively, and let $f^*$ be a maximum flow.
The graph underlying the network is connected, so we have that $n \leq m + 1$. Every time we find an augmenting path we increase the value of the flow by at least 1, meaning that $|f^*|$ (the value of the maximum flow) is an upper bound on the number of times the algorithm must find an augmenting path.
We can find an augmenting path by a simple graph traversal such as DFS or BFS which will take $O(m)$ time. Thus, we can bound the running time of Ford-Fulkerson as being at most $O(|f^*|m)$.
With especially bad choices of augmenting paths, this bound can actually be reached:
![](Pasted%20image%2020231009151158.png)
From this we conclude that Ford-Fulkerson is pseudo-polynomial-time, since its running time depends on both the size of the input and the value of a numeric parameter. This shows that the running time of Ford-Fulkerson can be very poor if the maximum flow is large and/or the augmenting paths are chosen poorly.

# The Edmonds-Karp Algorithm
This is a variation of the Ford-Fulkerson algorithm that utilises a simple technique to find good augmenting paths, which results in a faster running time. 
The technique is based on being "more greedy" than the previous solution; namely, at each iteration we choose an augmenting path with the minimal number of edges, which can be easily done in $O(m)$ time with a modified BFS traversal.
First, we introduce some extra notation:
- The **length** of a path $\pi$ is the number of edges in $\pi$.
- For a flow $f$ in some network $N$, define $d_f(v)$ as the minimum length of an augmenting path from the source to $v$. This is the **residual distance** of $v$ with respect to flow $f$.

We begin the analysis by noting that the residual distance is non-decreasing in a series of Edmonds-Karp augmentations:
![](Pasted%20image%2020231009152427.png)
There is a very long proof for this.
In other words, when we do an Edmonds-Karp augmentation, the residual distance from the source to any vertex can only increase or stay the same.
We can further show that the number of flow augmentations will be no more than $nm$.
![](Pasted%20image%2020231010110827.png)
![](Pasted%20image%2020231010110820.png)
Using the fact that a single augmentation can be done in $O(m)$, we can conclude that Edmonds-Karp computes a maximum flow in $O(nm^2)$ time.

# Maximum Bipartite Matching
In this problem, we are given a connected undirected graph with these properties:
- The vertices of $G$ are partitioned into two sets $X$ and $Y$.
- Every edge in $G$ has one endpoint in $X$ and the other in $Y$.

Graphs that fit this description are called **bipartite graphs**. A **matching** in $G$ is a set of edges that share no endpoints - this set pairs up vertices in $X$ with vertices in $Y$ such that each vertex has **at most one** "partner" in the other set. The maximum bipartite matching problem is to find a matching with the greatest number of edges.
![](Pasted%20image%2020231010111734.png)

## Reduction to the Maximum Flow Problem
Let $G$ be a bipartite graph whose vertices are partitioned into sets $X$ and $Y$. We can create a flow network $H$ such that a maximum flow of $H$ can be immediately converted into a maximum matching in $G$:
1. Include all the vertices of $G$ in $H$, plus a new source vertex $s$ and sink vertex $t$.
2. Add every edge in $G$ to $H$, but direct the edges such that they depart from the endpoint in $X$ and arrive at the endpoint in $Y$. In addition, insert a directed edge from $s$ to every vertex in $X$, and a directed edge from every vertex in $Y$ to $t$. Finally, assign each edge of $H$ a capacity of 1.

Now we calculate a maximum flow for $H$, and define a set $M$ of edges by taking every edge from $G$ where $f(e) = 1$. We assert that this set $M$ is a matching.
Consider the vertices in $Y$; they have an outgoing flow of exactly 1 because each vertex connects to the sink, and to achieve a max flow each of these edges will be fully utilised. Therefore, in order to satisfy the conservation rule each vertex in $Y$ must also have an incoming flow of exactly 1, meaning it must be paired with exactly one vertex from $X$.
Consider the vertices in $X$; each vertex has a single incoming edge with a capacity of 1, so they can have an incoming flow of either 0 or 1. If the incoming flow is 1, then they must send flow to exactly one vertex in $Y$. If the incoming flow is 0, then they cannot send any flow. Therefore, each vertex in $X$ is matched with either 1 or no vertices from $Y$.
Therefore, the set $M$ is indeed a matching.
![](Pasted%20image%2020231010113623.png)
# Baseball Elimination
Let $T$ be a set of teams in a baseball league. At any given point in the season, each team $i$ in $T$ will have some number of wins ($w_i$) and some number of games left to play ($g_i$). The **baseball elimination problem** is to determine if it is even possible for a team $i$ to finish the season given the games it has already won and the games it has left to play. This depends on more than just $w_i$ and $g_i$; it also depends on the schedules of every team involved.
Let $g_{i,j}$ represent the number of games remaining between team $i$ and team $j$, such that
$$g_i=\sum_{j\in T}{g_{i,j}}$$
(The number of games remaining for $i$ is the sum of the games remaining with each other team.)
For example:
![](Pasted%20image%2020231010114952.png)
With all the different ways a team $k$ can be eliminated, it might at first seem computationally infeasible to determine whether team $k$ is eliminated. However, this problem can also be reduced to a network flow problem.
Let $T'$ denote the set of teams other than $k$ ($T' = T - \{k\}$). Let $L$ denote the set of games that are left to play among the teams in $T'$. That is:
$$L = \{\{i,j\}:i,j \in T' \text{ and } g_{i,j} > 0\}$$
Finally, let $W$ denote the largest number of wins possible for team $k$ given the current standings ($w_k + g_k$).
If $W < w_i$ for some team $i$ then $k$ is immediately and directly eliminated, so let us assume that no single team eliminates team $k$. To consider how combinations of game outcomes might eliminate team $k$, we create a graph $G$ with this structure:
- Include the items in the sets $T'$ and $L$ as vertices, as well as a source $s$ and a sink $t$.
- For each game pair $\{i,j\}$ in $L$, add an edge $(s, \{i,j\})$ with capacity $g_{i,j}$. That is, connect the source to every game node and give the edge a capacity equal to the number of those games remaining.
- For each game pair $\{i,j\}$ in $L$, add the edges $(\{i,j\}, i)$ and $(\{i,j\}, j)$ with capacity $+\infty$. That is, connect each game node to both of the participating teams with edges of infinite capacity.
- For each team $i$, add an edge $(i, t)$ with capacity $W - w_i$. That is, connect each team node to the sink. Note that $W - w_i$ cannot be negative because we already ruled out the case where $W < w_i$ (a team is eliminated instantly by a single team's win count). 

![](Pasted%20image%2020231010120643.png)
