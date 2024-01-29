We can now find if a problem is decidable and therefore computationally solvable in theory, but there may be some problems that take so long to solve that they aren't feasible. In this section we look at how to analyse the time complexity of a Turing machine, and decide if it is computationally intractable.

# Measuring Complexity
Take the language $A=\{0^k1^k|k \leq 0\}$. Clearly this language is decidable, but how long will a single-tape Turing machine take to decide $A$? Let us examine the single-tape machine $M_1$ for $A$:
![](Pasted%20image%2020231026110827.png)
Note that we are describing the machine using quite low level descriptions, importantly including the movement of the head.
Note that when analysing the complexity of a turing machine we are only concerned with the length of the input *string*, and not the structure it represents (if any). 
![](Pasted%20image%2020231026111638.png)
Like usual, we are more concerned with **asymptotic analysis**. That is, we usually disregard every term in the complexity other than the highest order, because that term dominates the others on large inputs. In other words, it's the usual **big-O notation**.
![](Pasted%20image%2020231026111851.png)
![](Pasted%20image%2020231026112015.png)
Also note that the base of a logarithm does not need to be expressed in a big-O complexity. Because $$\log_bn = \log_2n/\log_2b,$$
The base is equivalent to a constant coefficient and can therefore be ignored like any other.

Sometimes big-O complexities can appear in arithmetic expressions, for example $f(n) = O(n^2) + O(n)$. In that case, the higher order term will cancel out the lower order one like usual and make the expression equivalent to $f(n) = O(n^2)$. Other cases include:
- $f(n) = 2^{O(n)}$, which can't be reduced but has a straight forward meaning ($2^{cn}$ for some $c$)
- $f(n) = 2^{O(\log n)}$, which can be transformed into $f(n) = n^{O(1)}$. This is called a **polynomial bound**.

We often talk of **polynomial bounds** like $f(n) = n^{O(1)}$, and **exponential bounds** like $f(n) = 2^{n^{O(1)}}$.

There is also **small-o notation**, which is equivalent to big-O notation but inclusive.
![](Pasted%20image%2020231026113332.png)
Also note that for a Turing machine, a linear time complexity is **impossible**.

Back to our analysis of $M_1$:
We consider its fours stages separately.
- Stage 1: The machine scans the entire tape to verify it is in the correct form, which takes $n$ steps. It then moves back to the left, ready for the next stage, which takes another $n$ steps and brings the total for this stage to $2n$ steps. In big-O, this is $O(n)$.
- Stage 2 & 3: A single iteration of Step 3 requires $n$ steps as it goes from one side of the tape to the other. As part of Stage 2, this will happen at most $(n/2)$ times because every sweep eliminates two characters. Therefore, the total time taken by stages 2 and 3 is $(n/2)n = O(n^2)$.
- Stage 4: The machine scans the entire tape to determine if there are any un-eliminated characters, which takes $n$ steps. This is $O(n)$.

In total, the running time is $O(n) + O(n^2) + O(n)$, or $O(n^2)$. This concludes our analysis of this machine.

Let's introduce some more notation for classifying languages according to their complexity:
![](Pasted%20image%2020231026114531.png)
For example, the language $A$ is in $\text{TIME}(n^2)$, because our analysis of $M_1$ showed that it ran in $O(n^2)$ time and $\text{TIME}(n^2)$ contains all languages that can be decided in $O(n^2)$ time.

Can $A$ be decided faster than $M_1$ can? Yes - here is the Turing machine that does it:
![](Pasted%20image%2020231026122145.png)
Skipping the analysis (which is simple), this machine runs in $O(n\log n)$ time.
So earlier we showed that $A \in \text{TIME}(n^2)$, but now we've found a better bound of $A \in \text{TIME}(n \log n)$. This is the **best result possible** for a single tape turing machine. We can actually show that any language decidable in $O(n \log n)$ time on a single tape machine is **regular**.
We can, in fact decide language $A$ in $O(n)$ time *if* we have a multi-tape machine. Here is the multi-tape machine that will do this:
![](Pasted%20image%2020231026122842.png)
Clearly this runs in $O(n)$ time. This running time is the best possible for a multi-tape machine.

Through some complicated proofs, we can say that:
- If an $O(t(n))$ multi-tape machine exists to decide $A$, then there is an equivalent single-tape machine that runs in $O(t^2(n))$.
- If an $O(t(n))$ non-deterministic (single-tape) machine exists to decide $A$, then there is an equivalent single-tape machine that runs in $2^{O(t(n))}$ time. (We consider the running time of a non-deterministic machine to be the running time of it's longest branch, as if all branches were running in parallel.)

# The class $\text{P}$
We have shown that there is at most a *polynomial* difference between the time complexity of a problem on a single-tape vs a multi-tape Turing machine. On the other hand, there is at most an *exponential* difference between the time complexity of a problem on a deterministic vs a non-deterministic Turing machine.

For our purposes, polynomial differences in running time can be considered small, while exponential differences can be considered large. Why separate the classes here?
The difference in growth rate between polynomials and exponentials is drastic: let $n$ be 1000, which is a reasonable input size for an algorithm. A machine that runs in $n^3$ time will take 1 billion steps, which is a lot but it is at least a feasible number. A machine that runs in $2^n$ time will take a number of steps so large that it has 300 digits in it. In many cases a polynomial algorithm will be fast enough, but there are very few cases where an exponential time algorithm will be useful.
Exponential algorithms usually arise when we solve a problem with a **brute-force search**. For example, we can find the prime factors of a number by searching through all possible divisors; the search space is exponential in size, so the search runs in exponential time. You can often avoid brute-force solutions through a deeper understanding of a problem, which can lead to a polynomial time algorithm of much greater utility.
All "reasonable" computational models are **polynomially equivalent** - that is, any one of them can simulate another with only a polynomial increase in running time. By "reasonable", we mean any model that approximates the running time of an algorithm on an actual computer. For example, single and multi tape Turing machines are "reasonable" models while a non-deterministic Turing machine is not.
The purpose of defining these classes of algorithms as polynomial, exponential, etc. is to allow us to think of the time complexity of an algorithm in a way that doesn't depend on the specific model of computation we are using; we are not concerned with how long a multi-tape Turing machine takes to run an algorithm, we are concerned with the inherent complexity of the algorithm regardless of model. By defining classes that are so large they encompass any possible variance from reasonable model to reasonable model, we can eliminate that variable entirely and classify algorithms independently from the machine they were analysed with.
This is the definition of the class $\text{P}$:
![](Pasted%20image%2020231027131559.png)
This follows from the previous conversation:
![](Pasted%20image%2020231027131752.png)
## Example problems in $\text{P}$
When presenting a polynomial time algorithm, we will give high-level descriptions without referencing features of any particular computational model. This avoids tedious details of tape and head movement. We continue listing algorithms in numbered stages. We must be sensitive to the number of Turing machine steps required for each stage, and the total number for all stages.
When analysing an algorithm, we must first set a polynomial upper bound on the number of stages that the algorithm uses. That is, the program can not be defined in such a way that it has more than a polynomial number of steps given an input of length $n$.
Then we examine the individual stages and ensure they can all be implemented in polynomial time in our model. We make sure when describing our algorithm that we choose stages that make this easy.
When we have confirmed both of these things, we can conclude that the algorithm runs in polynomial time.
Note that this also assumes that the encoding used for the input is reasonable. Most encodings, e.g. graphs, automata, numbers in most bases, are already known to be reasonable and scale polynomially or slower. There is an exception to note, in that numbers encoded in base 1 (unary) scale extremely fast and are not considered a reasonable representation.
Many of these example problems will concern graphs. One reasonable representation is a list of its nodes and edges. Another is an adjacency matrix. When analysing an algorithm on graphs we may compute the running time in terms of the number of nodes instead of the size of the representation; this is acceptable because in these reasonable encodings the size of the representation is polynomial in the number of nodes.

### PATH Problem
![](Pasted%20image%2020231027133631.png)
![](Pasted%20image%2020231027133642.png)
We assert that PATH is in class $\text{P}$.
![](Pasted%20image%2020231027133720.png)
Steps 1 and 4 only run once, and clearly run in polynomial time.
Step 3 requires a scan across the input, with some testing if nodes are marked. This is easily accomplished in polynomial time.
Step 2 consists of running Step 3 a maximum of $1 + 1 + m$ times, as on each run except the last a node is marked and the iteration ends when all edges are marked. Running the polynomial Step 3 a polynomial number of times is polynomial.
Therefore, $M$ is a polynomial time algorithm for PATH and PATH is in $\text{P}$.

# The class $\text{NP}$
There are many interesting and useful problems for which we have not found polynomial time algorithms. Why haven't we? The answer is that we don't know why - perhaps these problems *do* have polynomial time solutions and we simply haven't found them yet, or perhaps they simply *cannot* be solved in polynomial time due to some kind of intrinsic difficulty.
What we do know is that the complexities of many problems are linked. If a polynomial time algorithm is found for one of these problems, then that solution can be used to solve an entire group of related problems. To show this, let's begin with an example:
### Verifiability
A Hamiltonian path is a path in some directed graph $G$ that goes through every node exactly once. Here is a Hamiltonian path:
![](Pasted%20image%2020231027135754.png)
We describe the problem as such:
![](Pasted%20image%2020231027135717.png)
We can easily solve this in exponential time by taking the brute-force algorithm for PATH and adding a check that verifies the path is Hamiltonian. Nobody knows if this problem can be solved in polynomial time.
This problem, while not decidable in polynomial time, is **verifiable** in polynomial time. That is to say, you cannot check if a graph has a Hamiltonian cycle in polynomial time, but given a resulting Hamiltonian path from some external source you can verify if the graph does actually contain that path.
Another problem that is polynomially verifiable is **compositeness**. A natural number is *composite* if it is the product of two integers greater than 1. In other words, a composite number is a number that is not prime. We can define this problem:
![](Pasted%20image%2020231027140758.png)
We can easily verify that a number is composite given a divisor of that number, but deciding if a number is composite without that extra information is not so easy. There is a recently discovered algorithm that can actually test if a number is prime or composite in polynomial time, but it is very complicated compared to the method for verifying compositeness.
Some problems are not polynomially verifiable. For example, the inverse of HAMPATH $\overline{\text{HAMPATH}}$ cannot be verified in polynomial time - what would the result given to check even be?
We formally define this concept of a verifier:
![](Pasted%20image%2020231027141517.png)
A verifier takes in some additional info, represented by $c$ in the definition, and using this info checks if some string is a member of $A$. This information is called a **certificate**, or a **proof**, and it proves the string's membership in $A$. Note that this certificate is polynomially bounded in size, because the verifier will have to access it.
In HAMPATH, the certificate for some string $\langle G,s,t \rangle$ is a Hamiltonian path from $s$ to $t$, encoded in some sensible way. For the COMPOSITES problem, it is a divisor of the input number.
This leads to the definition of $\text{NP}$ we are using:
![](Pasted%20image%2020231027142104.png)
The name, **N**ondeterministic **P**olynomial time, is derived from the alternate definition (remember from Algorithms and Data Structures) where an algorithm is in $\text{NP}$ if it is solvable by a non-deterministic Turing machine in polynomial time.
Here is an example solution of HAMPATH that is in polynomial time, but using a nondeterministic Turing machine:
![](Pasted%20image%2020231027142628.png)
Each step clearly runs in polynomial time (for each branch of the computation), so the machine runs in nondeterministic polynomial time and HAMPATH is in $\text{NP}$.
We define the complexity class $\text{NTIME}$, which is the same as $\text{TIME}$ but for nondeterministic machines.
![](Pasted%20image%2020231027143007.png)

Here is an example of an NP problem:
### Clique Problem
A **clique** in an undirected graph is a subgraph such that every node is connected to every other. For example, here is a graph with the maximal clique highlighted:
![](Pasted%20image%2020231027143239.png)
A **k-clique** is a clique with exactly $k$ nodes. The previous example contains a 5-clique.
We formalise this problem:
![](Pasted%20image%2020231027143328.png)
We can solve this in one of two ways:
- Verifier: We show that there is a verifier for CLIQUE. That is, there is a deterministic machine that can determine if a given certificate is in CLIQUE. In this case our certificate will be a set of nodes, which we assert is a clique.
![](Pasted%20image%2020231027143543.png)
- Nondeterministic Machine: We can show that there is a nondeterministic Turing machine that solves the problem in polynomial time.
![](Pasted%20image%2020231027143627.png)

# $\text{P}$ vs $\text{NP}$
In summary:
![](Pasted%20image%2020231027143735.png)
It may seem that polynomial verifiability is much more powerful than polynomial decidability, but we do **not** know if it actually is; it is possible that $\text{P}$ and $\text{NP}$ are equal. We have not been able to prove there is a single language in $\text{NP}$ that is definitely not in $\text{P}$.
This question, if $\text{P} = \text{NP}$, is one of the greatest unsolved problems in theoretical computer science and contemporary mathematics. If these classes were equal then any $\text{NP}$ problem would be guaranteed to have some polynomial time solution. Most researchers do not believe the classes are equal, due to the massive amount of fruitless effort invested into finding polynomial time solutions for certain $\text{NP}$ problems. People have tried proving the inverse, that the classes are unequal, but that would require proving that there is no fast algorithm with the same power as brute-force, which is beyond scientific reach.

# The classes $\text{L}$ and $\text{NL}$
Until now we have concerned ourselves with time complexity, in which the minimum possible time complexity is linear due to the need to read in the entire input. When we talk about space complexity, we can discuss **sub-linear** bounds.
To analyse space complexity, we introduce a new Turing machine with two tapes: a read-only tape that contains exactly the input at all times and cannot be changed, and a read/write work tape. We give the machine some way to detect when the read head is at the left or right ends of the input, and say that it must remain on the portion of tape holding the input.
The work tape works in the usual manner, and the only cells that we count towards the space complexity are the ones on the work tape.
This model is equivalent to a one-tape model for space bounds of linear and above, but we can only use this model for sublinear space bounds.
We define new classes:
![](Pasted%20image%2020231027150503.png)
Here is an example of a language in $\text{L}$:
![](Pasted%20image%2020231027152344.png)
This language has previously been given with a single-tape Turing machine. That machine was linear in space, as it crossed 0s and 1s off as they were matched resulting in the entire input being worked on. We can implement another machine that is logarithmic in space using a two-tape machine by instead keeping a counter of the number of occurrences of 0s and 1s. Because a binary counter will be logarithmic, the algorithm will only use logarithmic space and therefore $A$ is in $\text{L}$.

Here is an example of a language in $\text{NL}$:
![](Pasted%20image%2020231027153349.png)
We know an algorithm that is in $\text{P}$, but that algorithm uses linear time. It is not known if PATH can be solved deterministically in logarithmic space, but we do know of a nondeterministic logarithmic space algorithm for PATH.
The nondeterministic machine starts at node $s$ and nondeterministically guesses the nodes of a path from $s$ to $t$. The machine need only record the position of the current node at each step on the work tape. It then nondeterministically selects the next node from those pointed at by the current node and repeats until it reaches $t$ and accepts or it has repeated $m$ times and rejects.
Therefore, PATH is in $\text{NL}$.
# Class Inclusions
![](Pasted%20image%2020240123150031.png)
![](Pasted%20image%2020240123150316.png)
![](Pasted%20image%2020240123150422.png)
![](Pasted%20image%2020240123150439.png)
![](Pasted%20image%2020240123150449.png)
From the f-Bounded halting problem (i think):
![](Pasted%20image%2020240123150918.png)
![](Pasted%20image%2020240123150926.png)
