Turing machines are as previously taught; machines with an infinite spool of tape representing memory. It can move around on the tape and read and write symbols to it. They are controlled by state machines, where the next action is decided by the current state and the symbol written on the tape below the read head. A turing machine either outputs *accept*, *reject*, or does not halt.
![](Pasted%20image%2020231016132215.png)
We define a Turing machine by its transition function $\delta$. $\delta$ takes the form $$Q \times \Gamma \rightarrow Q \times \Gamma \times \{L, R\}$$
$\delta(q,a) = (r,b,L)$, where
- $q$ is the current state
- $a$ is the symbol currently seen on the tape
- $r$ is the next state
- $b$ is the symbol that will be written over $a$
- $L$ or $R$ indicates which direction the head should move after writing $b$

The whole Turing machine can be defined like so:
![](Pasted%20image%2020231016133142.png)
The machine receives an input $w$ containing characters from $\Sigma$, which is written on the left of the tape and can contain no blank characters, so the first blank character is the end of the input. The head begins on the leftmost character, and computation proceeds according to the transition function. The computation continues until either the machine enters an *accept* or *reject* state, where it will halt. If that never happens, the computation continues forever.
# Configurations
The current position, state and tape contents of the machine at any given point is called a **configuration**. We can denote a configuration as $uqv$, where $q$ is a state and $u$ and $v$ are strings; $q$ is the current state, $u$ is the tape contents to the left of the head and $v$ is the tape contents below and to the right of the head. For example:
$$1011q_701111$$
Means that the machine is in state $q_7$, the tape holds the contents $101101111$, and the head is at the 5th position, over a $0$.
![](Pasted%20image%2020231016134050.png)
We say that a configuration $C_1$ **yields** a configuration $C_2$ if the machine goes from $C_1$ to $C_2$ in a single step.
Suppose we have a tape alphabet containing $a, b, c$, two strings from the alphabet $u$ and $v$, and two states $q_i$ and $q_j$. Take the two configurations $ua q_i bv$ and $uq_jacv$.
We can say that $ua q_i bv$ yields $uq_jacv$ if $\delta(q_i, b) = (q_j,c,L)$. You can say the same for configurations $ua q_i bv$ and $uac q_j v$, if $\delta(q_i, b) = (q_j,c,R)$.

There are two special cases: at the left edge, the tape changes but the head does not move. At the right edge (where the tape is blank) we can say that $uaq_i$ is equivalent to $uaq_i\textvisiblespace$ (i.e. we just add a space to the string).

Some names:
The **start configuration** on some input $w$ is $q_0w$, where the machine is in the start state $q_0$ and is all the way to the left of the tape.
An **accepting configuration** is one where the state is $q_\text{accept}$.
A **rejecting configuration** is one where the state is $q_\text{reject}$.
Both accepting and rejecting configurations are **halting configurations**, and yield no further configurations.
A machine **accepts** an input $w$ if there is a sequence of configurations $C_1,C_2,\dots,C_k$, where
- $C_1$ is the start configuration of $M$ with input $w$
- each $C_i$ yields $C_{i+1}$, and
- $C_k$ is an accepting configuration.

The set of strings that machine $M$ accepts is **the language of $M$**, denoted $L(M)$. The machine is said to "recognize" the language, and the language is said to be "Turing-recognizable".

The three outcomes of passing an input into a machine are *accept*, *reject* and *loop*, loop being the name for the machine never halting. Instead of considering these three states, you may group *reject* and *loop* together to get "accepts" and "fails to accept". A machine that never loops is called a **decider**; it "decides" all languages that it recognizes, and every language is decides is said to be "Turing-decidable" (or simply "decidable").

Here is an example of a turing machine, given formally:
![](Pasted%20image%2020231016141105.png)
![](Pasted%20image%2020231016141114.png)
![](Pasted%20image%2020231016141130.png)
![](Pasted%20image%2020231016141141.png)
![](Pasted%20image%2020231016141645.png)
# Variants of Turing Machines
There are some variants of Turing machines, such as multitape machines or nondeterministic machines. All of these variants have the same power as a regular Turing machine, which will be shown shortly. This property of Turing machines, that they maintain the same power in the face of certain changes in definition, is called **robustness**.
How do we show robustness? Take the example variant of a Turing machine where you can stay still, in addition to moving left or right. Can this recognize more languages, and thus increase the power of the model? For this one at least, the answer is obviously no - you can simulate the new "stay still" feature in the regular Turing machine by just moving back and forth.
This example contains the key to showing the equivalence of these variants. To show two models are equivalent, we show that one can simulate the other.
## Multitape Turing Machines
This variant is like an ordinary Turing machine, but with several tapes that each have their own head for reading and writing. The input appears initially on tape 1, with the others blank. The transition function is changed to give the required actions for every tape simultaneously. Formally, it is
![](Pasted%20image%2020231016144348.png)
where $k$ is the number of tapes. In other words, the function now takes the current state and the current tape symbol from **every roll of tape**, and returns the next state plus a symbol to draw and a move to take for **every roll of tape**. Here is an example expression:
![](Pasted%20image%2020231016144504.png)
Meaning: If the machine is in state $q_i$, and every head 1 to $k$ is reading symbol $a_1$ to $a_k$, change to state $q_j$, and each head writes the corresponding symbol and then does the corresponding movement.
This might seem more powerful than an ordinary Turing machine, but we can show they are equivalent.
### Equivalence Proof
We will show how to simulate a multitape machine $M$ with an ordinary machine $S$.
Say that $M$ has $k$ tapes. $S$ will simulate the effect of $k$ tapes by storing the information of every tape on its one tape.
We use the symbol \# (which we are assuming is not in the tape language) as a delimiter that separates the contents of the different logical tapes on our single real tape. We must also keep track of the locations of each head; we do this by adding a dot above one of the symbols on each logical tape, which we think of as a kind of virtual head. The dots are not a new feature, they are just new symbols that correspond to the non-dotted symbols.
Here is an illustration:
![](Pasted%20image%2020231016150856.png)
To maintain this structure during computation:
1. At the start of computation, $S$ puts it's tape into the format ![](Pasted%20image%2020231016151242.png). That is, the input on the first logical tape, an empty logical tape for each in $M$, and the first item of each logical tape marked, with a \# separating the tapes.
2. To simulate a single move, the head scans from the first \# to the final one in order to determine the symbols under the virtual heads. It then makes a second pass to update the tapes according to $M$'s transition function.
3. If $M$'s transition function dictates that a virtual head must move to the right, and $S$ encounters a \#, then the virtual head has moved onto what would be a blank space in $M$. $S$ writes a blank symbol onto this cell and then shifts the contents of the tape to the right. It then continues the simulation where it left off.

## Nondeterministic Turing Machines
A Turing machine, but at any point in a computation the machine may proceed in several directions. The transition function for this variant has the form
![](Pasted%20image%2020231016152016.png)
That is, the same as a regular transition function but for each state and symbol there can be many resulting actions to take.
The computation of this nondeterministic Turing machine is a tree whose branches correspond to the different possible actions that the machine could have taken. If any of the branches leads to the accept state, the machine accepts the input. This variant also does not affect the power of the model.

### Equivalence Proof
The idea for having an ordinary Turing machine $D$ simulate this nondeterministic variant $N$ is to have $D$ try all possible branches of $N$'s computation. If $D$ ever finds the accept state, then it accepts. Otherwise, $D$ will not terminate.
Represent $N$'s computation for some input $w$ as a tree, with each tree branch representing a branch of the nondeterminism. The nodes of the tree represent a configuration of $N$, and the root is the start configuration. We want $D$ to search this tree for an accepting configuration.
A bad idea would be a depth-first search; there is a possibility that a single infinite branch is explored and $D$ never halts, even though another branch held an accepting configuration. Hence, we use a breadth-first search, which guarantees $D$ will visit every node in the tree until it encounters an accepting configuration.

In order to do the proof, we will actually use a multitape Turing machine as $D$ - as per the previous proof, this is equivalent to an ordinary one.
![](Pasted%20image%2020231016153157.png)
The input tape represents the input to $N$ which is never altered, the simulation tape contains $N$'s tape along the current branch of computation, and the address tape keeps track of the current location in $N$'s computation tree.
(Would take too long to copy this perfectly good explanation, so here's a screenshot:)
![](Pasted%20image%2020231016153413.png)
We can modify this proof so that if $N$ always halts on every branch of computation, then $D$ will also always halt. If this is true then $N$ is a decider.
This leads to this assertion:
![](Pasted%20image%2020231016153622.png)

## Enumerators
An enumerator is a Turing machine with an attached printer. At any point the Turing machine may send a string to this printer, which is added to the infinite page of outputs.
![](Pasted%20image%2020231017110319.png)
An enumerator $E$ starts with a blank work tape. If the enumerator doesn't halt then it may print an infinite list of strings, but if it does halt then collection of strings it prints out is the **language enumerated by $E$**. $E$ can generate these strings in any order, and potentially with repetitions.
This leads to this theorem:
![](Pasted%20image%2020231017110700.png)

# Equivalence with other models
There are many other models of general purpose computation other than Turing machines, and some of them are quite different. However, they all share the essential feature of Turing machines in that they have unrestricted access to unlimited memory, which distinguishes them from weaker models such as finite automata. Remarkably, *all* models with this feature are equivalent in power so long as they satisfy some reasonable requirements.

# The definition of "Algorithm"
Informally speaking, an algorithm is a collection of instructions to carry out some task. While this intuitive description was acceptable for a time, it became insufficient when mathematicians aimed to gain a deeper understanding of algorithms. Therefore, a more rigorous definition is needed.

## Hilbert's 10th Problem
Hilbert's 10th problem is a problem regarding polynomials: the objective is *to test if a polynomial has an integral root* (that is, a root with integral values for all variables).
At the time he posed this problem, he simply assumed that a procedure for solving this problem must exist, but it is in fact **impossible** to create an algorithm that solves this problem. With the contemporary (1900s) understanding of algorithms it would have been impossible to arrive at this absolute conclusion that there is no algorithm. Indeed, proving that a problem does or does not have an algorithm that can solve it requires a clear definition of "an algorithm".
This framework was provided in 1936, when Alonzo Church and Alan Turing released papers regarding their own representations of algorithms. Alonzo Church had represented them using his $\lambda$-calculus, while Turing had represented them using his "machines". These two definitions were shown to be equivalent, and the combination of the two led to the precise definition of algorithms known as the **Church-Turing thesis**.

## The solution
![](Pasted%20image%2020231017112904.png)

# Describing Turing Machines
We are now comfortable with the idea that Turing machines capture all algorithms. Now we standardize the way we describe Turing machine implementations of algorithms.
Turing machines work on strings, but usually the input of an algorithm is not a string. For some object $O$, we give a string encoding $\langle O \rangle$ that can be given to a Turing machine. The Turing machine must decode this string representation in order to execute the algorithm. We are not concerned with which encoding is used.
We format our descriptions similarly to previous examples, with the machine being equal to an English description of the algorithm in quotes. For example, here is the Turing machine that solves the "connected graph" problem
![](Pasted%20image%2020231017113709.png)
Here is a high level description of the Turing machine $M$ that decides $A$:
![](Pasted%20image%2020231017113747.png)
Notice that:
- The input must be specified as either a string or a string encoding of an object. There is an implicit step at the beginning where the machine checks the validity of the input.
- Indentations are used to indicate block structure of the algorithm.

More specifically, what is a valid encoding of a graph into a string? Here is one such encoding:
![](Pasted%20image%2020231017114047.png)
The encoding begins with a bracketed list of every node in the graph, then a list of every edge as pairs of endpoints.
Generally you will not have to describe the low-level implementation of an algorithm with regards to how the Turing machine specifically executes it.

# Decidability
This section includes some examples of languages that are decidable by algorithms, with a focus on languages concerning automata and grammars.

## Acceptance problem for DFAs
This problem concerns testing whether a particular DFA accepts a given string, and can be expressed as the language $A_\text{DFA}$, which contains every pair of possible DFA and accepted input:
![](Pasted%20image%2020231017115122.png)
Answering the acceptance problem for some DFA $B$ and input $w$ is the same as testing if $\langle B,w \rangle$ is a member of the language $A_\text{DFA}$.
In the following theorem we show that this language is decidable, and hence the acceptance problem for DFAs is decidable.
![](Pasted%20image%2020231017115454.png)
You can say the same about the acceptance problem for NFAs, but including a new first step where the given NFA is converted to a DFA.
The same approach suggests that the problem of determining if a regular expression generates a given string is decidable; Simply convert the regular expression to an NFA, then convert that to a DFA, then run the same procedure on that. You can use the description of the previous machine in the description of the next, if you like (i.e. machine $P$ decides the regex problem by converting the given regex to an NFA, then giving it to machine $N$ to solve, which converts the given NFA to a DFA and gives it to machine $D$ to solve).

## Emptiness testing for DFA
In this problem, we must determine if a DFA accepts any strings at all. Let the language be
![](Pasted%20image%2020231017120109.png)
i.e. the set of all DFAs with a non-empty language.
![](Pasted%20image%2020231017120238.png)

## Equivalence for DFA
In this problem, we must determine if two DFAs recognize the same language. Let
![](Pasted%20image%2020231017120354.png)
i.e. The set of all pairs of DFAs with equal languages.
![](Pasted%20image%2020231017120636.png)

## Etc
This section goes on for ages and doesn't really seem to do much but give examples.
BASICALLY, you can make a Turing machine that decides regular and context-free languages. Therefore, the hierarchy of languages looks like this:
![](Pasted%20image%2020231017120850.png)

# Undecidability
Undecidability is a property of a problem in that it is algorithmically unsolvable. This is much more important of a property to be able to determine that Decidability, as it shows the inherent limitation of computation.
There are some important unsolvable problems outside the realm of mathematical theory. Consider the problem of "software verification" - that is, given a program and a precise specification of what it should do, verify that the program is correct. You may hope that because the program and the specification can be expressed as mathematically precise objects, you could automate this process, but the problem is completely undecidable.
Here is an introductory example:
We want to determine if a Turing machine accepts a given input string. Let
![](Pasted%20image%2020231017121548.png)
This may look similar to the acceptance problems for DFAs and CFGs, but it is **not decidable**.
![](Pasted%20image%2020231017121639.png)
![](Pasted%20image%2020231017121831.png)
![](Pasted%20image%2020231017121839.png)
![](Pasted%20image%2020231017122051.png)
(There's no point doing any more copying, just read the notes. They're fine.)
Conclusion:
![](Pasted%20image%2020231017122730.png)
We can now prove that $A_\text{TM}$ is undecidable.
![](Pasted%20image%2020231017123214.png)
(Yeah just read the notes ive got nothing to contribute)