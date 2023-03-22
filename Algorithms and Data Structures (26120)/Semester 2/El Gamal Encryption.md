## Public/Private Key Cryptography
A one-way function is a function that is easy to compute but hard to invert.
Formally, a function $f$ is one-way if it can be computed in polynomial time, but another polynomial time random algorithm $F$ that attempts to compute a pseudo-inverse for $f$ will succeed with negligible probability.
The aim is to make it hard in the average case, not the worst case.

El Gamal Encryption is based on this principle, and an observation on this equation:
$y = a^b \text{ mod } p$
It is easy to compute $y$ in this equation. However, even if you know $y$, $a$ and $p$ it is average case "hard" to find $b$.

## Public Keys
El Gamal encryption creates public keys by finding the **primitive roots module n** of a prime. (Note: the exam does not require being able to find primitive roots, only that you know what they are. You **will** need to know how to check if two numbers are relatively prime, which can be done using **Euclid's Algorithm**.)

## Encrypting Messages
To encrypt a message using El Gamal Encryption, you first need to find some random number $k$ which is relatively prime to $p - 1$ ($p$ being a prime that appears in the public key). You then compute $a^b \text{ mod } k$ for various numbers; to do this you will need a **fast modular exponentiation** algorithm.

## Decrypting Messages
To Decrypt the message, you need to be able to find the inverse modulo some prime of a number. You can do this with **Euclid's Extended Algorithm**, or you can use **fast modular exponentiation** for this too.

## Euclid's Algorithm
The **greatest common divisor** (gcd) or **highest common factor** (hcf) of two numbers is the largest number that divides them both. if $gcd(a, b) = 1$ (that is, they have no common factors other than 1) then we say that $a$ and $b$ are **relatively prime**. But how to calculate the gcd? Take this recursive definition:
$$gcd(a, b) = gcd(b, a \text{ mod } b)$$
or, as pseudocode:
```python
algorithm gcd(a, b):
	if b == 0: return a
	else: return gcd(b, a mod b)
```