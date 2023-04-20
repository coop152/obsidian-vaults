In order to have secure encryption, we need a way to quickly find prime numbers.
## Prime Number Theorem
![](Pasted%20image%2020230420142035.png)
So if we randomly pick a (large) number $n$ then we know there is a $\frac{\pi(n)}{n}$ chance that it is prime (that is, the number of prime numbers less/equal to $n$ divided by the number of numbers in general less/equal to $n$). That is, the chance it is prime is $\frac{1}{\ln{n}}$.
If we want a prime of the same **size** as $n$ (that is, the same number of bits) then if we pick $\ln{n}$ random numbers of that size, it is probable we will find a prime.
With this method, finding a 1024 bit prime number will take approximately $\ln{2^{1024}} \approx 710$ attempts.
## Checking primes
Because our method of finding primes depends entirely on checking if a number is prime (as opposed to generating them), the difficulty of finding prime numbers depends entirely on the difficulty of checking if a random number is prime.
#### Trial Division (Naive algorithm)
```python
alg isPrime(x):
	y = x - 1
	while y > 0:
		if x % y == 0: # if x is divisible by y
			break
		y = y - 1
	if y == 1:
		return True
	else return False
```
A small optimisation can be had by starting at $y = \sqrt{x}$.
What is the complexity of this algorithm? 
To check a single number, using the optimisation, the algorithm is $O(\sqrt{x})$.
We are concerned with numbers of a certain bit length, so substitute $x$ for $2^n$.
With regards to the bit length of the input number, the algorithm is $O(\sqrt{2^n})$, or $O(2^{\frac{n}{2}})$. Therefore, this method is not useful as it is too complex.
## Exploiting Fermat's Little Theorem
Recall that if $p$ is prime and $x$ is an integer such that $x \text{ mod } p \neq 0$ then $x^{p - 1} \equiv 1(\text{ mod } p)$.
Meaning, we could use **fast modular exponentiation** which is of linear complexity.
So, we pick an $x$ at random and use fast modular exponentiation to find $x^{p - 1} \text{ mod } p$. If $x^{p - 1} \text{ mod } p \neq 1$ then we know that $p$ is not prime.
But what if $x^{p - 1} \equiv 1(\text{ mod } p)$? Could we make it work if we picked several different values for $x$?
#### Carmichael Numbers
Unfortunately, there exists a set of numbers called the **Carmichael Numbers** for which $x^{n - 1} \equiv 1(\text{ mod } n)$ for all $1 \leq x \leq n - 1$, but $n$ is composite. Examples of Carmichael numbers include 561 and 1105. Therefore, we cannot simply try many values of $x$ until we find a prime number.
## Randomised Primality Testing
#### The Witness
Assume we have a function $\text{witness}(x, n)$ that works as follows:
- If $n$ is prime, then $\text{witness}(x, n)$ returns false
- If $n$ is composite, then $\text{witness}(x, n)$ returns false with probability $q < 1$.
#### The Algorithm
Where $n$ is the number to be tested, and $k$ is a *confidence parameter* which determines how confident we will be in our result.
![](Pasted%20image%2020230420144751.png)
What is $t$?
- The algorithm is supposed to return false (indicating that $n$ is prime) with an error probability of $2^{-k}$ ($k$ being the confidence parameter).
- It will return an incorrect answer with probability $q^t$ (assuming our $x$s are picked independently)
- Essentially, we are forcing the probability of an error down below the confidence parameter by repeatedly applying the algorithm on some random input.

The calculation for $t$ comes from this:
![](Pasted%20image%2020230420145408.png)
Lets apply the calculation to some numbers and observe the results:
$k = 3$ and $q = 0.5$
So, the confidence parameter is 3 and the probability of correctly identifying a composite is 0.5. In this, we are saying that we want to know that $n$ is prime with a probability of 0.125 ($2^{-k}$) or less that the answer is wrong.
With these values, $t = 3$. Therefore, we need to iterate three times in order to reduce the error probability to 0.125.
## The Miller-Rabin Primality Test
So we have a way to efficiently test for primes given some witness function, but we need a witness function to use it. To start out, lets use the method from before which exploits Fermat's Little Theorem. That is, we use fast modular exponentiation to check if $x^{n - 1} \text{ mod } n \equiv 1$. This will always correctly identify a prime, and it will *usually* identify a composite with some chance $q$ of a false prime if $n$ is a Carmichael number. 

In fact, we can improve on this. If $p$ is prime and $x^2 \text{ mod } p \equiv 1$, then it must be true that either $x \equiv 1 (\text{ mod } p)$ or $x \equiv -1 (\text{ mod } p)$.
Moreover, if $n$ is odd then $n - 1 = 2^tm$ for some odd number $m$. 
