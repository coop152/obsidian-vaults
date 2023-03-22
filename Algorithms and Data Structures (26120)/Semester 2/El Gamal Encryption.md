## Public/Private Key Cryptography
A one-way function is a function that is easy to compute but hard to invert.
Formally, a function $f$ is one-way if it can be computed in polynomial time, but another polynomial time random algorithm $F$ that attempts to compute a pseudo-inverse for $f$ will succeed with negligible probability.
The aim is to make it hard in the average case, not the worst case.

El Gamal Encryption is based on this principle, and an observation on this equation:
$y = a^b \text{ mod } p$
It is easy to compute $y$ in this equation. However, even if you know $y$, $a$ and $p$ it is average case "hard" to find $b$.

## Public Keys
El Gamal encryption creates public keys by finding the **primitive roots modulo n** of a prime. (Note: the exam does not require being able to find primitive roots, only that you know what they are. You **will** need to know how to check if two numbers are relatively prime, which can be done using **Euclid's Algorithm**.)

## Encrypting Messages
To encrypt a message using El Gamal Encryption, you first need to find some random number $k$ which is relatively prime to $p - 1$ ($p$ being a prime that appears in the public key). You then compute $a^b \text{ mod } k$ for various numbers; to do this you will need a **fast modular exponentiation** algorithm.

## Decrypting Messages
To Decrypt the message, you need to be able to find the "inverse modulo prime $p$" of a number. You can do this with **Euclid's Extended Algorithm**, or you can use **fast modular exponentiation** for this too.

## Euclid's Algorithm
The **greatest common divisor** (gcd) or **highest common factor** (hcf) of two numbers is the largest number that divides them both. if $gcd(a, b) = 1$ (that is, they have no common factors other than 1) then we say that $a$ and $b$ are **relatively prime**. But how to calculate the gcd? Use **Euclid's Algorithm**, a recursive definition of the gcd:
$$gcd(a, b) = gcd(b, a \text{ mod } b)$$
or, as pseudocode:
```python
algorithm gcd(a, b):
	if b == 0: return a
	else: return gcd(b, a mod b)
```
If you were calculating this in an exam, you might keep a table like this:
![](Pasted%20image%2020230322122134.png)

## Modular Arithmetic
$$\mathbb{Z}_n = \{0, 1,\dots, n-1\}$$
We have the set $\mathbb{Z}_n$ of all integers from $0$ to $n-1$. This set is the set of results that you can get from $a \text{ mod } n$, where a is some integer. Doing maths on this set (as opposed to the full set of integers) has some useful properties:
- To add, $(a + b) \text{ mod } n$. To multiply, $(a \times b) \text{ mod } n$.
- $(a + b) \text{ mod } n \equiv (a \text{ mod } n + b \text{ mod } n) \text{ mod } n$
- $ab\text{ mod }n \equiv (a\text{ mod }n)(b\text{ mod }n)\text{ mod }n$
- Multiplicative inverses are integers, not fractions
	- e.g. $5 \times 2 \text{ mod } 9 \equiv 1$
	- But they don't always exist.

Alternatively, we can stipulate that all numbers in the set must be prime:
$$p \text{ is prime, } \mathbb{z}_p=\{0, 1, \dots, p-1\}$$
It is always true that $gcd(x, n) = 1$ if and only if $x$ has a multiplicative inverse in $\mathbb{Z}_n$. So multiplicative inverses exist for all numbers except 0, modulo prime numbers.
$\mathbb{Z}_n^*$ is the set of numbers less than $n$ that have multiplicative inverses modulo $n$. There is also $\mathbb{Z}_p^*$, the same set for primes.
#### Generators
Consider the set where 7 is our prime number. What happens if we look at all the exponents of 2 modulo 7?
$2^1=2, 2^2=4, 2^3=1, 2^4=2$ and then it repeats.
We say that the order of 2 in $\mathbb{Z}_7^*$ is 3, from the 3 unique values you can get from exponentiating it.
Now consider the exponents of 3 modulo 7:
$3^1 = 3, 3^2=2, 3^3=6, 3^4=4, 3^5=5, 3^6=1$
The order of 3 in $\mathbb{Z}_7^*$ is 6, and it gives every element in $\mathbb{Z}_7^*$. We call these numbers *generators*. In this case, 3 is a generator of $\mathbb{Z}_7^*$, or a *primitive root modulo 7*.
For any prime number $p$ there exists a generator.
(Again, you do not need to know how to find the primitive roots of a prime number.)

#### Public Key Generation
In El Gamal Encryption, someone picks a prime $p$ and a primitive root of that prime $g$.
The recipient of a message picks a private key $x \in \mathbb{Z}_p^*$ (that is, some number that has a multiplicative inverse modulo $p$). The public key is:
$$(p, g, g^x \text{ mod } p)$$
Let's work through an example.
The sender picks these values:
$p = 7, g = 3$ (in the real world, a much bigger prime would be used.)
The recipient must pick a private key. They decide upon $x = 3$. They keep this to themselves.
Now they calculate $g^x \text{ mod } p$:
$3^3 \text{ mod } 7 = 2$
Thus the public key is $(7, 3, 2)$. This is accessible to anyone.
#### Fast Modular Exponentiation
Here are two representations of the same algorithm:
![](Pasted%20image%2020230322130522.png)
(They look different but they're actually doing the same thing. You can ignore the c in the second one, it's just there from the textbook for analysis reasons.)
Through means that I do not understand, they calculate $a^p \text{ mod } n$. (Just watch the video)
#### Discrete Logarithm
The discrete logarithm of some integer $y$ to the base $b$ is an integer $x$ such that
$$b^x \equiv y \text{ mod } n$$
This is the inverse of modular exponentiation. Crucially, there is **no fast algorithm** for computing these. This is why El Gamal is one-way.

#### Bring-together
If we have someone's public key $(p, g, y)$ and we want to encrypt a number $M$ we follow these steps:
1. Pick a random integer $k$ which is relatively prime to $p-1$.
2. Compute $a \leftarrow g^k \text{ mod } p$
3. Compute $b \leftarrow My^k \text{ mod } p$
4. Send $(a, b)$.

## Calculating multiplicative inverses
(random theory i dont understand)
![](Pasted%20image%2020230322133341.png)
Essentially the same as Euclid's but returns some extra values $i$ and $j$.
Watch the video for an example.

## Full procedure
#### Encryption
Watch the video for the full example including working.
Let's send an encrypted message with El Gamal. Before anything is sent, the recipient must generate a public key by picking a prime $p$, a primitive root of that prime $2$, and a random private key $x$.
$p = 13$
$g = 2$
(In an exam question, you will be given a primitive root.)
$x = 5$
The public key is $(p, q, g^x \text{ mod } p)$. To find $y$ ($g^x \text{ mod } p$) we use **Fast Modular Exponentiation**.
This gives the recipient a public key of $(13, 2, 6)$. They give this to the sender, which allows them to encrypt their message.
Now the sender has the recipient's public key. They want to send the message $M=10$. First, they must pick a number $k$ that is relatively prime to the $p$ in the public key.
$k = 7$
This is trivial for such small numbers. In a real situation with large prime numbers, you might randomly generate values of $k$ and then use **Euclid's Algorithm** to check that they are relatively prime, retrying until you get something usable.
Now we must calculate $g^k \text{ mod } p$ and $My^k \text{ mod } p$:
$g^k \text{ mod } p = 2^7 \text{ mod } 13 = 11$
$My^k \text{ mod } p = 10\cdot6^7 \text{ mod } 13 = 5$
Our encrypted message is $(11, 5)$. We send this to the recipient, who then decrypts it:
#### Decryption
The recipient gets the message from the sender. They have all of this information, in total:
$p = 13, g = 2, x = 5$
$a, b = (11, 5)$ (The message)
To decrypt this message we will:
1. Find $a^x \text{ mod } p$
2. Find the multiplicative inverse of that
3. Multiply it by $b$

**Find $a^x \text{ mod } p$**:
$11^5 \text{ mod } 13 = 7$
**Find the multiplicative inverse of 7**:
This step can be done with **Euclid's Extended Algorithm** or with **Fast Modular Exponentiation**.
With Euclid:
![](Pasted%20image%2020230322140738.png)
With Fast Modular Exponentiation:
![](Pasted%20image%2020230322140942.png)
The multiplicative inverse is 2. ($(7 \cdot 2) \text{ mod } 13 = 1$)
**Multiply 2 by $b$**:
$2 \cdot 5 = 10$.
The decrypted message is 10.