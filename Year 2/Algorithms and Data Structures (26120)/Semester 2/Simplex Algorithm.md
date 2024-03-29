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
So we have this representation of the problem:
![](Pasted%20image%2020230307121839.png)
First, we have to pick the column that we want to pivot on. We use a simple heuristic; pick the objective function's largest value (that is, the one with the highest magnitude) and we decide to pivot on that column. In this example $-6$ is the largest, so we pivot on the $x_2$ column.
![](Pasted%20image%2020230307122308.png)
Now that we've chosen which non-basic variable we're going to swap, we have to chose the basic variable to swap it with. For each entry in the column, divide the value at the right by the value in the selected column; this is the **slack**. We select the row with the smallest slack, making the intersection of the selected column and row the **pivot**. In this example, we choose the $s_1$ row.
![](Pasted%20image%2020230307122511.png)
We call $x_2$ the "entering variable" and $s_1$ the "leaving variable". In the next matrix, we will replace the leaving variable with the entering variable.
Our next objective is to manipulate this column such that the selected pivot has a value of 1 and the other values in the column are 0. We can only change each row by adding (or subtracting) multiples of the selected row, meaning many values that aren't in the selected row or column will also be changed. 
In this example, the pivot is already 1 so we don't need to change it. We must reduce the value in the second row by 1, so we replace it with $R_2 - 1R_1$. We must reduce the value in the third row by 5, so we replace it with $R_3 - 5R_1$. We must also increase the value in the objective row by 6, so we replace it with $R_\text{Obj} +6R_1$.
![](Pasted%20image%2020230307123454.png)
At the end of this iteration, we can read off a basic solution from the right of the matrices:
When $x_2 = 11, s_2 = 16, s_3 = 25$, 
the objective function (or the value of $z$, if you like) has become $66$.
This solution is better than the initial one at the origin, and as you can see it is feasible:
![](Pasted%20image%2020230307123721.png)
We repeat this pivoting process until we reach the optimal solution; you know you have reached the optimal solution when there are no more negative numbers in the objective row.
We ended up with this from the last iteration:
![](Pasted%20image%2020230307123837.png)
We select $x_1$, as it's the only negative value:
![](Pasted%20image%2020230307123920.png)
Some more rules are shown here:
- You must select a negative number when deciding on the pivot column.
- You cannot pivot on a row with negative slack. (in this example, the first row where slack is $\frac{11}{-1}$.)

The $s_3$ row has the smallest slack, so we select this row for a pivot and then pivot. We divide the third row by seven to make the pivot equal 1, then make the other values 0 by adding multiples of the pivot row:
![](Pasted%20image%2020230307124309.png)
We now have another basic solution, better than the last. However we still aren't finished, as there is still a negative value in the objective row. Some other notes:
- You can use the pivot row from either the old or new table if you would like. Usually using the new table is easier, since you just made it equal to 1, but this may not always be the case (such as in this example, where some similar fractions appear in the old table.)

With this in mind, we apply the final iteration, using the old value of the row instead to simplify calculations:
![](Pasted%20image%2020230307124817.png)
There are no more negative numbers in the objective row; this is the optimal basic solution. We can read off the optimal values to get:
$x_1 = 15, x_2 = 12$
$s_1 = 14, s_2 = 0, s_3 = 0$
$z = 132$
The slack values are irrelevant to the original problem, so they can be discarded. 
#### Why do we stop pivoting?
Our rule is that we stop pivoting when the objective row contains no negative values. For example, this means we must continue:
![](Pasted%20image%2020230307125247.png)
And this means we are finished:
![](Pasted%20image%2020230307125406.png)
But why does this work? Consider that these rows represent equations. That first example can be written as:
$z -10x_1 + 0x_2 + 6s_1 + 0s_2 + 0s_3 = 66$
Remember the non-negativity constraints; $x_1$ and $x_2$ is either 0 or a positive number; a negative coefficient in this formula is subtracting a positive number from $z$, thus reducing $z$'s value. If we want to maximise $z$, we therefore need to remove these negative coefficients.

## Special Cases
#### The origin isn't a feasible solution (Artificial Variables)
Consider the following example:
![](Pasted%20image%2020230314135806.png)
The second constraint removes the origin from the set of feasible solutions. The graph looks like this:
![](Pasted%20image%2020230314135846.png)
If we convert this to slack form and try to solve using simplex, we get this tableaux:
![](Pasted%20image%2020230314135924.png)
Our basic solution does **not** satisfy the non-negativity constraints.
The solution is to add some new variables called **artificial variables** that will allow $x_1=0, x_2=0$ to be a valid solution to any of the equations where this problem arises. We then penalise the inclusion of these variables in the the objective function, so that they will go to 0 when the optimal solution is reached.
So, lets add an artificial variable to the problem equation:
![](Pasted%20image%2020230314140426.png)
We add this artificial variable to the objective function, multiplied by a coefficient $M_1$ which we take to be arbitrarily large. Here is the tableaux:
![](Pasted%20image%2020230314141315.png)
First step is to pivot on the column with $M_1$ in it. The only row with a usable slack is the second one (and it will always be the one corresponding to the equation with the artificial variable in it), so pivot on that row.
![](Pasted%20image%2020230314141554.png)
Remember that $M_1$ is an arbitrarily large number. We now begin simplex as usual, in this case picking $-3-2M_1$ as the pivot column as it is the most negative (highest negative amount of the arbitrarily large $M_1$).
![](Pasted%20image%2020230314142208.png)
And then:
![](Pasted%20image%2020230314142331.png)
The final solution is $x_1=10, x_2=0$. The value of the artificial variable can be ignored.
#### The solution space is unbounded
Consider this example:
![](Pasted%20image%2020230314142639.png)
When drawn as a graph, it looks like this:
![](Pasted%20image%2020230314142717.png)
You can see that the solution space goes on forever. If you attempt to solve this with the regular method, you will eventually reach a situation where all of the slacks are negative, thus making it unsolvable. Simplex cannot solve unbounded problems; if you encounter one, it is most likely that a constraint is missing.
You can tell if the solution space is unbounded if there exists a valid entering variable (i.e. a column with a negative value in the objective row) where all of the co-efficients in that column are negative. For example:
![](Pasted%20image%2020230314150537.png)
You can tell this is an unbounded problem because $x_1$ is a valid entering variable but all of its coefficients are negative.
#### There is zero slack (degeneracy)
It is possible while doing Simplex to encounter a row with a slack of 0. 
(Just watch the video for this one, I can't write notes for this that are any more useful than the video)
[Click here for video](zeroSlackVideo.mp4)
The rule:
You must pick a row with zero slack **if** the co-efficient for the entering variable is positive (the number in the pivot column on that row). If none of the zero slack rows have a positive entering variable, then we choose the entering variable like usual (i.e. pick the one with the smallest positive slack).

