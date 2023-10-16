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
The machine receives an input $w$ containing characters from $\Sigma$, which is written on the left of the tape and can contain no blank characters, so the first blank character is the end of the input. 