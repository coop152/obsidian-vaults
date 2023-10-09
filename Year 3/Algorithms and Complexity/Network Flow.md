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

Alternatively, we create a new directed graph $R_f$ from $G$ by taking the same set of vertices and only adding edges where $\Delta_f(u,v) > 0$.