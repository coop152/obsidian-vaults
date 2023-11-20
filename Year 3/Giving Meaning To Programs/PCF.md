The simply typed lambda-calculus is an unrealistic programming language; while it does have nice properties such as strong normalization that make it theoretically useful, it is notably lacking features. Most important among these is **recursion**.
Instead, we think of the simply typed lambda-calculus as a template which can be modified to make many different languages. Most importantly, it embodies the fundamental operation of substitution and how it interacts with typing, which is central to any functional language. We will define a new language PCF by adding and removing some aspects of the simply typed lambda-calculus. Before we get to adding recursion, we will consider a few other changes.
# Built in data
The simply typed lambda-calculus lacks any built in datatypes. This is nice theoretically because it makes proofs easier and more general, but it is extremely obstructive when writing programs. In PCF, instead of the base type $\iota$ we have a concrete base type "nat" which models the natural numbers. 
To write numbers we add two constants $\bar{0}$ and $\bar{s}$ which represent 0 and the successor function. We write a natural number $n$ like ![](Pasted%20image%2020231120151940.png)
Because we aim to use recursion, we need to be able to determine if a number is $\bar{0}$ or a successor of something, and if it is then we want to be able to find the number it succeeds. For this we add the built-in operations `ifz` and `pred` to the language.
`ifz` takes three `nat` arguments. If the first argument is $\bar{0}$ then it returns the first argument, and returns the third argument otherwise.
`pred` is the predecessor function (which outputs $\bar{0}$ if given $\bar{0}$).
The typing rules for these are as follows:
![](Pasted%20image%2020231120152309.png)
