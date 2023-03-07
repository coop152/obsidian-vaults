
Take this example of a linear programming problem:
![](Pasted%20image%2020230307114018.png)
First, we convert this into **slack form**:
Each of these formulae are inequalities, so they have a sort of "slack"; in the first equation, $-x_1 + x_2$ could equal 11, or 10, or 9, etc. In order to convert into slack form we want to convert every equation into an equality, so how do we represent this slack without an inequality? Introduce a **slack variable** to represent the amount that the left hand side of the equation is falling short of the right hand side:
![](Pasted%20image%2020230307115306.png)
Using these equalities instead of the previous inequalities gives this slack form problem:
![](Pasted%20image%2020230307115344.png)
A problem is in **slack form** if all of the constraints are equalities (apart from the non-negativity constraints), and each equation contains a **slack variable** which appears only in that equation and whose coefficient is 1.
Some more terminology:
- Slack variables are also called "basic variables"
- The other variables are called "non-basic variables"
- The solution where every non-basic variable is zero is called the "basic solution"

## Pivoting
Now that we have our problem in slack form, we can use a technique called **pivoting**. First, add every slack variable to every equation. Continuing on from the previous example:
![](Pasted%20image%2020230307121242.png)
Lets also rewrite the objective function to make it match the format of the other equations:
![](Pasted%20image%2020230307121354.png)
We can now represent this as an augmented matrix:
![](Pasted%20image%2020230307121445.png)
And here is the objective function (note that the coefficient of the $z$ is omitted):
![](Pasted%20image%2020230307121520.png)
