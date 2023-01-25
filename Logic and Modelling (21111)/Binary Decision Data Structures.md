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
- Boolean operations are easy to implement - See boolean functions section.

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

#### Applying boolean functions to OBDDs
Suppose you have the boolean function for disjunction:
$$f(x_1, \dots, x_n) \stackrel{\text{def}}{=} x_1 \vee \dots \vee x_n$$
And you also have a series of OBDDs $D_1,\dots, D_n$ representing formulae $F_1,\dots,F_n$.
How do you calculate $f(F_1, \dots, F_n)$?
Assuming all of these OBDDs are in a global DAG, such that all isomorphic subgraphs are shared...
We can use the fact that If-Then-Else commutes on functions:
$$\begin{align*} f(\text{if } p \text{ then } l_1 \text{ else } r_1, \dots, \text{if } p \text{ then } l_n \text{ else } r_n) =&\\ \text{if } p \text{ then } f(l_1, \dots, l_n) \text{ else } f(r_1, \dots, r_n) \end{align*}$$
For example, say you have the OBDDs $D_1, D_2$ representing functions $F_1, F_2$. To get $F_1 \lor F_2$, consider that each of these OBDDs begins with an "If-Then-Else", in the form of the first node that tests a variable:
$f(F_1, F_2)$
$= f(\text{if } p \text{ then } l_1 \text{ else } r_1, \text{if } p \text{ then } l_2 \text{ else } r_2)$
$= \text{if } p \text{ then } f(l_1, l_2) \text{ else } f(r_1, r_2)$
While this still isn't solved, it's enough to start making the OBDD. add a node that tests $p$. If $p$ is true, go to $f(l_1, l_2)$ (which is still unknown as of yet) and vice versa for false. Repeat this process on the two functions until you reach the terminals, where the behaviour is decided by the function; for example with disjunction, a formula $\lor$ $1$ is always $1$, and a formula $\lor$ $0$ is always $0$. Here is the full method for disjunction:
![](Pasted%20image%2020230125125719.png)
The first line checks if any of the formulae are $1$; if so, the whole disjunction is $1$ and that is returned.
The second line checks for an empty disjunction, which is always false.
The third line checks for a disjunction of a single formula, which is just the formula.
The fourth/fifth lines remove any formulae that are $0$.
The function then picks the max variable that occurs in any of the formulae. A new node is made from this variable, with the true branch leading to the disjunction of all of the true branches in the formulae, and vice versa for the false branch.
The newly constructed disjunctions are then integrated into the global DAG.


## QBF OBDDs
Using these properties of QBFs, we can integrate quantified formulas into OBDDs:
- $\exists{p}(\text{if } P \text{ then } F \text{ else } G) \equiv F \lor G$
- $\forall{p}(\text{if } P \text{ then } F \text{ else } G) \equiv F \land G$
