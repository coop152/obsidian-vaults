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
If 