A frequent problem in finite difference based methods is the need to solve systems of equations described by a diagonal matrix $A$, where $Ax=b$. 
There are a number of methods for solving such systems, including matrix inversion, Gaussian elimination, and LU decomposition.
Notable is that finite difference equations often result in tridiagonal systems. For example:
![](Pasted%20image%2020240321151726.png)
This is a **tri-diagonal system**. All terms are **banded** together on the diagonal, with a maximum band width of 3. The general form is:
![](Pasted%20image%2020240321151819.png)
These systems are common in engineering and physical systems, and can be stored efficiently by omitting all items not on the diagonal.
We also have an algorithm that can efficiently solve these systems, called **Thomas Algorithm**.
# Thomas Algorithm
- Step 1: Decompose A