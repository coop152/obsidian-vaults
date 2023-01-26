Let $\mathbb{S} = (S, In, T, X, dom)$ be a transition system and $s$ be a state from $S$. The computation tree for $\mathbb{S}$ starting at s is the following (possibly infinite) tree.

1.  the nodes of the tree are labeled by states in $S$
2.  The root of the tree is labeled by $s$
3.  For every node in the tree, its children must follow it in the transition table
## Definitions
$$\begin{align*} \bigcirc &\text{ next} \\ \Box &\text{ always (in future)}\\ \Diamond &\text{ eventually (in future)}\\ \mathcal{U} &\text{ until}\\ \mathcal{R} &\text{ release} \end{align*}$$
