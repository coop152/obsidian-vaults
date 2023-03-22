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
