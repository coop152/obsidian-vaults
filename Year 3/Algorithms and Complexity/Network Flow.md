Consider a computer network modelled by a digraph $G$. Each vertex represents a computer, each edge represents a one-way communication channel from one computer to another, and the weight of each edge represents the *bandwidth* of that channel (i.e. the maximum number of bytes that can be sent along that channel per second.)
Suppose we want to send a high-bandwidth stream of data from some computer $s$ to some computer $t$, even higher bandwidth than the highest bandwidth single link in the network. This might seem impossible at first, but it may be possible by splitting our stream into packets and routing these packets through multiple paths in the network.
We formulate this problem by imagining that each edge in $G$ represents a "pipe" that can transport some commodity (in this case, packets) with the weight of that edge representing the maximum amount it can transport per unit time. When formulated like this, the problem is known as the **maximum flow problem**.

# Maximum Flow Problem
We are given a weighted, directed graph $G$ and asked to find a way of transporting the maximum amount of the given commodity from some vertex $s$, called the *source*, to some vertex $t$, called the *sink*.
![](Pasted%20image%2020231009110636.png)
The maximum flow problem is closely related to the **maximum matching problem**, where you find the maximum number of ways to match vertices of one type in a graph to vertices of another type. The maximum flow problem can be used to efficiently solve the maximum matching problem, which will be shown subsequently.
Sometimes in solving the problem we may have many different maximum flows. Although they may be equal in the flow they produce (i.e. the bandwidth they provide) they may be different in cost. We will also study methods for computing the maximum flow of minimum cost.

# Flows and Cuts
A flow network $N$ consists of the following:
- A connected digraph $G$ with non-negative integer weights on the edges. The weight of an edge $e$ is called the **capacity** $c(e)$ of $e$.
- 