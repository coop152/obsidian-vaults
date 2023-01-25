## Binary Decision Tree (BDT)
The BDT is a data structure for verifying interpretations against a formula. It is more efficient and faster than a truth table.
![](Pasted%20image%2020230124152622.png)
Circle nodes represent a decision; the solid line means the variable is true, and the dotted line means it is false.
Square nodes are "terminal nodes"; they represent the final result of evaluating the formula at the given interpretation.
#### Properties
-   Checking truth in an interpretation can be done **in linear time** in the number of variables.
-   Satisfiability checking can be done **in linear time** in the size of the tree.
-   Validity checking can be done **in linear time** in the size of the tree.
-   Equivalence checking is **hard**.
-   Some boolean operations, e.g. conjunction, are **hard to implement**.
-   The size of the tree is in the **worst case exponential** in the number of variables.

## If-Then-Else Normal Form
- The only connectives are if-then-else, and the terminals $\top$ and $\bot$.
- In the formula $\text{if } A \text{ then } B \text{ else } C$, $A$ is always atomic.
- Every propositional formula can be transformed into an equivalent If-Then-Else normal form.

## Binary Decision Diagram (BDD)
A BDD is a directed graph with no cycles that serves the same purpose as a BDT, but:
- It contains no redundant tests
- It has no isomorphic subtrees
![](Pasted%20image%2020230124160526.png)
#### Properties
- Satisfiability checking can be done **in constant time**.
- Validity checking can be done **in constant time** - must have only one node with the value 1.
- Equivalence checking is **hard**.
- Some boolean operations, e.g. conjunction, are **hard to implement**.
#### Converting from BDT to BDD
![](70q0u9.gif)

## Ordered Boolean Decision Diagram (OBDD)
Now introduce an order ($<$) on the variables in the formulae, and execute all tests in this order. The result of this is that:
- Every boolean function has a unique representation as an OBDD - OBDDs are **canonical** for boolean functions.
- Satisfiability can be checked **in constant time** - Does the node representing the formula connect to the 1 terminal?
- Validity can be checked **in constant time** - Is the node representing the formula the 1 terminal?
- Equivalence can be checked **in constant time** - Do both formulae start at the same node?
- Boolean operations are easy to implement - See boolean operation section.

#### Integrating a node into a DAG
![](Pasted%20image%2020230125120332.png)
In English:
`integrate(n1, p, n2, D)` is a function that takes two nodes, along with a propositional variable, and either inserts a new node into the global DAG or returns an equivalent existing one. The new/existing node will go to `n1` when `p` is true, and to `n2` when `p` is false. It goes as follows:
1. Are both of the destination nodes the same? In that case, just return one of those two nodes - You would always end up there no matter the value of `p`.
2. Does the global DAG already contain a node that fits the description of the one we're going to make? If so, return that node instead of making a new one.
3. Otherwise, make the new node and add it to the DAG, then return this new node.

#### Building an OBDD from a formula
![](Pasted%20image%2020230125121254.png)
In English:
`obdd(F)` is a function that takes a propositional formula `F` and adds it to the global DAG.
1. Simplify the formula. If it simplifies down to $\bot$, return the 0 node. If it simplifies down to $\top$, return the 1 node.
2. Otherwise, select the highest variable in the formula. This variable is the one that will be in the topmost node of this section.
3. Create the child OBDDs for the true and false branches, by replacing the variable by $\top$ or $\bot$ and running the function on that new formula.
4. Use `integrate()` to insert this new node into the global DAG.
