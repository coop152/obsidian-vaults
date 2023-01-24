Apply repeated transformations to a propositional formula in order to discover its **satisfiability**. If at least one branch can be found which resolves to true, it is satisfiable. If all branches resolve to true, it is valid. If all branches resolve to false, it is unsatisfiable.

## Example
![](Pasted%20image%2020230123115809.png)
This formula is **satisfiable**, as at least one of the branches resolves to true.

![](Pasted%20image%2020230123120250.png)
This formula is **unsatisfiable**, as all of the branches resolve to false.

## Algorithm
![](Pasted%20image%2020230123120130.png)
In plain English:
> "First, simplify the formula. If it simplified to true, it is satisfiable. If it simplified to false, it is unsatisfiable. If it still contains variables, select one of the variables using some common sense, and either 1 or 0. Apply splitting to the new formula created by replacing the variable with true or false. If this new formula is satisfiable, then so is the whole formula. If not, try again with the opposite boolean."