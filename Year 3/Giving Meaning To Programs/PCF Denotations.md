# Posets
Posets are sets that are partially ordered. A group of posets that we are concerned with are the *flat types*, for example $\mathbb{N}_\bot$ (the flat natural numbers). These sets are equal to the set indicated in their name but feature another element $\bot$ for which every element is greater:
![](Pasted%20image%2020231204134215.png)
We are also concerned with the function types that stem from these. The logic we use to construct these is that we are ordering elements by how "informative" they are: in a base type like natural numbers, no number is more informative than another but every number is certainly more informative than no result at all ($\bot$). For functions, we say that a function is more informative than another if if provides exactly the same results as that function **and then some**. For example:
![](Pasted%20image%2020231204134448.png)
![](Pasted%20image%2020231204134508.png)
Each of these functions is more informative than the last in the family because it returns one more result on top of the results of the previous. You cannot compare the informativity of these functions to another different function (say, one that always returns 1), only to themselves.
We can also observe an interesting property about this family of functions in particular:
![](Pasted%20image%2020231204135252.png)
This is interesting, because each of these functions $f_n$ is 'finite' by themselves (in the sense that they only return informative output for a finite number of inputs) but taken together they 'approximate' $f_\infty$. We will elaborate on this idea, but first we need to investigate partial orders more closely.
## Properties of partial orders
If we want to precisely define the notion of approximation then a particularly important idea is that of the "least upper bound" of a set of elements. First we define some properties that we expect of a least upper bound.
![](Pasted%20image%2020231204140156.png)
