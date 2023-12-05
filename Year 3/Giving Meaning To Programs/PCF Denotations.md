# Posets (jump ahead to chapter 4)
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
This is just the basic definition of a least upper bound.
![](Pasted%20image%2020231204140442.png)
(a): The least upper bound of a subset is less than or equal to the least upper bound of the whole set.
(b): If for every element in one subset $S$ there is something greater in another subset $S'$, then the least upper bound of $S$ is at most that of $S'$.
We introduce a notation for the least upper bound of a subset $S$:
$$\bigvee S$$
![](Pasted%20image%2020231204141141.png)
(a): The least upper bound of the union of many subsets is the same as the least upper bound of the individual least upper bounds.
(b): If the union of some group of subsets is equal to the union of another group of subsets, then the least upper bounds of the unions (and therefore the groups) are equal also.
## Posets of functions
To interpret function types we have to think about how we may put a partial order on a set of functions, and which functions we should consider. We only want to allow functions that are well-behaved w.r.t the partial order, so we impose two conditions:
First, we only consider **order-preserving** functions. This models the crucial idea that we can't possibly get more information from a computation that hasn't finished. We can wait for information to come in, but if it never comes then we can't supply information that would have resulted from it. In a more explicit sense, if a function expects a natural number and receives $\bot$ then it cannot possibly determine that the given number "won't terminate" - that's the halting problem. All it can do it wait indefinitely for a result, which would mean it also "returns" $\bot$. 
![](Pasted%20image%2020231204142418.png)
If our input and output sets are flat sets such as $\mathbb{N}_\bot$ then the condition is simply that
![](Pasted%20image%2020231204142833.png)
For example, our function $f_n$ is order-preserving because no matter which function in that family you choose, $\bot$ simply maps to $\bot$.
You can think of this condition as demanding that if we give some information to $f$ (in the form of $p$) and then give it at least that much or more (in the form of $p'$) then it must give at least as much information back for $p'$ as it did for $p$.
Another simple example of an order-preserving function is every constant function. That is, for posets $P$ and $Q$, where $q$ is some constant in $Q$:
![](Pasted%20image%2020231204143441.png)
is order-preserving.
![](Pasted%20image%2020231204143537.png)
Clearly the identity function on a poset is order preserving because it maps $\bot$ to $\bot$. There is a proof for the second part, but it is clear either way.
For two posets $(P, \leq)$ and $(Q,\leq)$ we write
$$P \Rightarrow_\leq Q$$
For the set of all order-preserving functions from $P$ to $Q$. We define a partial order on this set as follows:
![](Pasted%20image%2020231204144115.png)
In other words, a function $g$ is greater or equal to a function $f$ (in the pointwise order) if it gives a greater or equal result for all possible inputs.
![](Pasted%20image%2020231204145034.png)
Previously we had only defined $\bot$ for the base type, but we need to define it for *all* types: We can make any type reduce forever if we apply rec to its identity function, meaning any type can give no information. Therefore, we also use $\bot$ as the least element of any such poset:
![](Pasted%20image%2020231204150739.png)
# Directed-complete Partial Orders (DCPO)
So far we have only looked at posets with finite 'height'. You might assume that all of the posets we are concerned with are like this, but unfortunately not.
We intent to interpret `nat` using the flat natural numbers $\mathbb{N}_\bot$ and then use a function space construction to interpret higher types. The posets we obtain in this manner contain infinite chains, for example:
![](Pasted%20image%2020231204151238.png)
For the previously described $f_n$. We want to consider $f_n$ as approximating $f_\infty$, which is the identity on $\mathbb{N}_\bot$. All of these functions appear in our interpretation of `nat` $\rightarrow$ `nat`, so if we wanted to interpret a function of even higher type (e.g. one that takes or returns this) then we need to decide what elements of that function space should do with these infinite approximations.
(Some shit i don't really understand)
![](Pasted%20image%2020231204151841.png)
In other words, if we pick a finite number of elements in $S$ then there is another element that is greater than or equal to all of them.
To talk about the infinite objects approximated by finite computations, we have to restrict ourselves to special posets, namely those where we can always form a least upper bound to model the infinite object that they describe.
![](Pasted%20image%2020231204152141.png)
![](Pasted%20image%2020231204152424.png)
We can establish that the partial orders suggested for the base type are dcpos: The only directed sets in these flat posets have at most two elements (For example, $\{\bot, n\}$ for all $n$ in $\mathbb{N}_\bot$), and therefore a greatest element and a least upper bound.
![](Pasted%20image%2020231204152753.png)
In particular, $\mathbb{N}_\bot$ is a dcpo.
## Suitable functions and function spaces
To ensure that we're able to interpret recursion we need to demand an addition property for recursive functions: they have to be well-behaved with respect to the least upper bounds of directed sets.
![](Pasted%20image%2020231204153247.png)
![](Pasted%20image%2020231204153749.png)
In other words, a function between dcpos is Scott-continuous if applying the function to the least upper bound of some (directed) subset gives the same result as the least upper bound of applying it to every function.
You can also write this equality as
![](Pasted%20image%2020231204154056.png)
(Dont really get the rest of the stuff here)
![](Pasted%20image%2020231205114115.png)
The second condition we impose on functions we use in out interpretation is that they must be Scott-continuous.
If $D$ and $E$ are dcpos we write $D \Rightarrow E$ for the set of all Scott-continuous functions from $D$ to $E$.
![](Pasted%20image%2020231205114756.png)
![](Pasted%20image%2020231205114909.png)
![](Pasted%20image%2020231205114916.png)
## Fixed Points
To interpret the `rec` operator, we have to ensure there is suitable infrastructure in our model. Previously we argued that the meaning of `rec` must be a 'fixed point' in it's argument. In a normal set there is no guarantee that every function has a fixed point (consider the `not` operation on booleans), but now we have added the infrastructure of a dcpo and restricted ourselves to order-preserving Scott-continuous functions, which allows us to  solve this problem.
![](Pasted%20image%2020231205115544.png)
In other words, the fixed point of a function is the value for which the output is equal to the input. For example, the fixed point of the `square` function is 1 and the fixed points of $$f(x) = \lfloor x/2 \rfloor + 1$$ are 1 and 2.
We show the key result, that we always have fixed points for Scott-continuous functions on dcpos:
![](Pasted%20image%2020231205120602.png)
![](Pasted%20image%2020231205120938.png)
![](Pasted%20image%2020231205120947.png)
In the process of interpreting PCF terms we will apply the fixed point operator to interpretations of subterms, and we should think about its type, which is $$D \Rightarrow D \rightarrow D.$$
We name this operator `fix`. In these terms, the property of being a fixed point becomes for $f:D\rightarrow D$, the equality $f(\text{fix}f) = \text{fix}f$ holds.
![](Pasted%20image%2020231205121311.png)
We then ask if the fixed point we get from the operator is actually an operator in our model. That is, whether it is Scott-continuous.
![](Pasted%20image%2020231205121422.png)
And it is. We then show that the operator itself is Scott-continuous:
![](Pasted%20image%2020231205121611.png)
And it is.
# Denoting PCF (back to chapter 3)
So, what was the point of all that complicated set maths? If we want to define the interpretation of PCF then we need a significant amount of those results. Namely:
- The hardest syntactic construction to interpret is `rec`, for which we make use of fixed points. In order to ensure that these always exist, we also have to use dcpos.
- We use function space construction to model function types like we did in the simply typed lambda-calculus, but because we are using dcpos it is more sophisticated.

So, we choose a suitable dcpo $D$ as the base type. With this, we may define
![](Pasted%20image%2020231205122012.png)
To interpret the remaining types.
Essentially this is the same approach as modelling that we took with the simply typed lambda-calculus, in that a function type is defined by the set of function between the interpretations of the subtypes. The three main differences are the following:
- We introduce $\bot$, a value which we may think of meaning 'undefined' or 'no information'.
- Introducing this value requires us to define a partial order, so we only allow functions that are well behaved with respect to this partial order. In the language of chapter 4, we only allow Scott-continuous functions.
- Unlike the simply typed lambda-calculus which had a base type interpreted by an arbitrary set, PCF will have a fixed base type interpreted by the set $\mathbb{N}_\bot$.

The first requirement is needed because in a sufficiently powerful language, some programs will not terminate (this is the Halting Problem). The second requirement is necessary for our fixed point operator to work, which in turn allows `rec` to be interpreted. The third requirement is to ensure we can model the constructions $\bar{s}$, $\text{pred}$ and $\text{ifz}$, which are clearly based on a natural-like type.
Remember that we do not consider the number 1 as less than or equal to the number 2, for example. The relation between members of the base type conveys how much information each element conveys compared to the other, not their numeric value.
![](Pasted%20image%2020231205124443.png)
We use brackets $[\![ \ ]\!]$ with no superscript to refer to this interpretation.
## Denoting types and terms
Like with the simply typed lambda-calculus, we start with interpreting the types for our language. This ends up looking similar to the interpretation of function types in the simply typed lambda-calculus, but the set of functions defined by $\Rightarrow$ is not the same as the set defined by $\rightarrow$. The result is a **partially ordered set**, with some badly-behaved functions omitted.
![](Pasted%20image%2020231205124757.png)
Now we consider how to interpret the terms:
- For terms and term constructors involving `nat`, we use corresponding infrastructure for $\mathbb{N}_\bot$. In particular, $\bar{0}$ is interpreted as $0$, and $\bar{s}$, $\text{pred}$ and $\text{ifz}$ are interpreted as analogous functions we define in the following definition.
- For variables, abstractions and applications we use essentially the same interpretations as in the simply typed lambda-calculus.
- For rec we use the fixed point construction as previously described.

These are the functions we will define, which we hadn't already:
![](Pasted%20image%2020231205125815.png)
First we ensure these functions live in our intended model (that is, they are Scott-continuous).
<Proof goes here (not that I'd need to know it anyway)>

Here is a summary of the many cases for denoting a a PCF term:
![](Pasted%20image%2020231205130228.png)
![](Pasted%20image%2020231205130240.png)
Consider this example, which involves an infinitely recursive term of type `nat`:
![](Pasted%20image%2020231205130830.png)
This shows how $\bot$ factors into the denotation of a type, but consider this more interesting term:
![](Pasted%20image%2020231205131023.png)
![](Pasted%20image%2020231205131035.png)
