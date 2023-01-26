Let $\mathbb{S} = (S, In, T, X, dom)$ be a transition system and $s$ be a state from $S$. The computation tree for $\mathbb{S}$ starting at s is the following (possibly infinite) tree.

## Computation Tree
1.  the nodes of the tree are labeled by states in $S$
2.  The root of the tree is labeled by $s$
3.  For every node in the tree, its children must follow it in the transition table.
![](Pasted%20image%2020230126003407.png)
## Definitions
$$\begin{align*} \bigcirc &\text{ next} \\ \Box &\text{ always (in future)}\\ \Diamond &\text{ eventually (in future)}\\ \mathcal{U} &\text{ until}\\ \mathcal{R} &\text{ release} \end{align*}$$
![](Pasted%20image%2020230126003454.png)
$\bigcirc F$: $F$ will be true in the next state.
$\Box F$: $F$ will be true indefinitely.
$\Diamond F$: $F$ will be true eventually.
$F \mathcal{U} G$: $F$ will be true until G is true. 
$F \mathcal{R} G$: $F$ will eventually become true, wherein it will release $G$ from being true.

## Equivalences
$$¬\bigcirc F \equiv \bigcirc ¬F$$
$$¬\Box F \equiv \Diamond ¬F$$
$$¬\Diamond F \equiv \Box ¬F$$
$$¬(F \mathcal{U} G) \equiv ¬F \mathcal{R} ¬G$$