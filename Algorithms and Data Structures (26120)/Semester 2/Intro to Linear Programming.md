Linear Programming is an algorithm that allows you to find the optimal value for some algebraic expression while following a system of constraints. For example:
> A farmer has a field of size $A$ square metres. He wants to know how much barley he should plant ($x_b$) and how much wheat he should plant ($x_w$) in order to maximise profit.
> The sale price of one square metre of barley is $S_b$, and the sale price of one square metre of wheat is $S_w$.

Therefore, in this case the objective of the problem is to maximise the total sale price of both crops, i.e. to maximise $S_bx_b + S_wx_w$.
This is subject to some constraints; he only has $A$ square metres of field to plant in. Therefore, $x_b + x_w \leq A$.
> The farmer has some amount of pesticide ($P$) and fertiliser ($F$) left over in his barn from the last harvest, and he doesn't want to buy any more. The amount of fertiliser he would need to grow each crop is $F_b$ and $F_w$ respectively, and $P_b/P_w$ for the pesticide.

These constraints can be represented as this:
$F_bx_b + F_wx_w \leq F$
$P_bx_b + P_wx_w \leq P$
Therefore this entire problem can be represented like this:
Maximise $S_bx_b + S_wx_w$
$x_b + x_w \leq A$
$F_bx_b + F_wx_w \leq F$
$P_bx_b + P_wx_w \leq P$
Any linear programming problem can be expressed as linear relationships. The constraints are inequalities (always $\leq$), and all variables must be greater than or equal to zero.

## Visualising as a Graph
This problem, having two variables, can be represented as a graph. This may be helpful for envisioning the problem, though you'd never do this in practise. Lets take the problem for some arbitrary values $A=4,F=30,P=30,F_b=2,F_w=10,P_b=10,P_w=2,S_b=5,S_w=4$
Maximise $5x_b + 4x_w$
$x_b + x_w \leq 4$
$2x_b + 10x_w \leq 30$
$10x_b + 2x_w \leq 30$
(and implicitly, $x_b, x_w \geq 0$)
If you take $x_b$ and $x_w$ as the two axes of the graph, you can plot the constraints as lines, with the exclusive area (or the inclusive area, if you like) shaded in. In this graph, $x_w$ is horizontal and $x_b$ is vertical:
![](Pasted%20image%2020230228131812.png)
