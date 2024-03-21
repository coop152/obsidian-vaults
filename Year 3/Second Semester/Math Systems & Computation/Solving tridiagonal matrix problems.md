A frequent problem in finite difference based methods is the need to solve systems of equations described by a diagonal matrix $A$, where $Ax=b$. 
There are a number of methods for solving such systems, including matrix inversion, Gaussian elimination, and LU decomposition.
Notable is that finite difference equations often result in tridiagonal systems. For example:
![](Pasted%20image%2020240321151726.png)
This is a **tri-diagonal system**. All terms are **banded** together on the diagonal, with a maximum band width of 3. The general form is:
![](Pasted%20image%2020240321151819.png)
These systems are common in engineering and physical systems, and can be stored efficiently by omitting all items not on the diagonal.
We also have an algorithm that can efficiently solve these systems, called **Thomas Algorithm**.
# Thomas Algorithm
**Step 1: Decompose A**.
This involves transforming A according to this pseudocode:
![](Pasted%20image%2020240321152211.png)
In plain English: Copy A. Iterate over each row in this new copy, starting from the second row. For each row, where $\alpha$ is the number left of the diagonal and $\beta$ is the number on the diagonal:
- Divide $\alpha$ by the number above it
- Multiply the new value of $\alpha$ by the number above $\beta$, then subtract that from $\beta$.

Do this until you've completed the entire matrix. For example:
![](Pasted%20image%2020240321152805.png)
![](Pasted%20image%2020240321163203.png)
![](Pasted%20image%2020240321163212.png)
**Step 2: Forward Substitution.**
Next, transform $s$ according to this pseudocode:
![](Pasted%20image%2020240321163352.png)
In plain English: Iterate over every row in $s/A$. Multiply that row's $\alpha$ by the previous row's $s$, then subtract that from the current row's $s$.