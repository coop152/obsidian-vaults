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
This variant is like an ordinary Turing machine, but with several tapes that each have their own head for reading and writing. The input appears initially on tape 1, with the others blank. The transition function is changed to give the required actions for every tape simultaneously