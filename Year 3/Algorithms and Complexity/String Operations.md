A character string is a simple sequence of characters. For example:
```
P = "CGTAAACTGCTTTAATCAAACGC"
R = "U.S. Lands an Astronaut on Mars!"
S = "http://www.wiley.com/college/goodrich/".
```
P represents a DNA sequence, R represents text to be read by a person (e.g. on a website) and S represents a URL to a website.
Here are some of the many operations you might perform on strings:
# Substrings
Many string processing operations involve breaking a string down into smaller parts. We call these substrings. The strict definition for a substring of some string $P$ (of length $m$) is a string of the form $$P[i]P[i+1]\dots P[j]$$ for some $i$ and $j$ such that $0 \leq i \leq j \leq m - 1$. The indexes in this definition are **inclusive**.
This definition technically includes a string in its own set of substrings; If this is not desired, then you want a **proper substring**, which is the same but either $i > 0$ or $j<m-1$.
There is a simplified notation for substrings: $$P[i..j]=P[i]P[i+1]\dots P[j].$$
By convention, if $i>j$ then the substring is equal to the **null string**, which has 0 length. We also define some special cases of substrings:
- $P[0..i]$, for $0 \leq i \leq m-1$: This is a **prefix**.
- $P[i..m-1]$ for $0 \leq i \leq m-1$: This is a **suffix**.

The null string is a prefix and suffix of any other string.

# Pattern Matching
We are given a text string $T$ (length $n$) and a pattern string $P$ (length $m$), and we want to find if $P$ is a substring of $T$. We have found a match if there is a substring of $T$ at some index $i$ such that $$T[i]=P[0], T[i+1]=P[1], \dots, T[i+m-1]=P[m-1].$$
The output of the algorithm is either an indication that the pattern was not found, or the starting index in $T$ of the matching substring.
The representation of character strings being used does not have a specific character set in mind, so instead we represent the set of characters that can be used with the **alphabet** $\Sigma$. We usually assume that $\Sigma$ is a finite set, meaning that $|\Sigma|$ is a fixed constant.
## Brute Force
The simplest solution is to just test every possible placement of $P$ relative to $T$.
```python
algorithm BruteForceMatch(str T, str P):
	for i = 0 to T.size - P.size:  # try every index where the strings overlap
		j = 0
		# while the pattern string is not complete and it matches the text
		while j < P.size and T[i + j] == P[j]:
			j++
		if j == m:  # did we match the entire pattern?
			return i
	return "No substring of T matching P"
```
The correctness of this algorithm is obvious.
This solution has a bad complexity; For each possible index of P in T, we perform up to $m$ character comparisons in the worst case. As there are $n - m + 1$ possible indices, in the absolute worst case our running time is $O((n-m+1)m)$, which is $O(nm)$. In the case that $m=n/2$, it actually becomes $O(n^2)$.

## Knuth-Morris-Pratt Algorithm (KMP)
This algorithm involves preprocessing the pattern string $P$ in order to compute a **failure function** $f$. This function informs us of a proper shift of $P$ such that, as much as possible, we can reuse previously performed comparisons.
Specifically, the failure function $f(j)$ gives the length of the longest prefix of $P$ that is also a suffix of $P[1..j]$ (take note, starting at 1 and not 0.) This failure function is important because it "encodes" repeated substrings inside the pattern itself. Here is an example $f(j)$:
![](Pasted%20image%2020231003140523.png)
In the context of KMP, the output of $f(j - 1)$ represents how many characters we can skip comparing if the match failed on character $j$ of the pattern. For example, if the current substring of T has matched P up until $j = 5$ but failed ("abacab"), then you can start checking one character after the start because there is a one character overlap between the end of the substring you know to match ("abaca") and the start of the pattern.

Here is the KMP algorithm, which uses the failure function to find a match:
```python
algorithm KMPMatch(str T, str P):
	Func f = KMPFailureFunction(P)  # find the failure function for P
	i = 0
	j = 0
	while i < T.length:
		if P[j] == T[i]:  # matching char in text and pattern
			if j == P.length - 1:  # finished the whole pattern
				return i - P.length + 1  # match found
			i++
			j++
		else if j > 0:  # no match, but we did match a bit of the start
			j = f(j - 1)  # we can skip this many comparisons
		else:
			i++
	return "No substring of T matching P"
```

Here is an example run, using the pattern for which we found the failure function prior:
![](Pasted%20image%2020231003144205.png)
### Complexity
For now, forget about the time taken to compute the failure function.
The running time of the algorithm is proportional to the number of iterations of the while loop. For the sake of the analysis, let's define $k = i - j$, which is the total amount by which $P$ has been shifted with respect to $T$. Note that $k \leq n$.
We have these three cases for each iteration of the loop:
- If $T[i] = P[j]$, then $i$ increases by 1, $j$ increases by 1, and $k$ does not change.
- If $T[i] \neq P[j]$ and $j>0$, then $i$ does not change and $k$ increases by at least 1 (but maybe more). In this case $k$ changes from $i - j$ to $i - f(j-1)$, which is an increase of $j - f(j-1)$. We know this to be positive because $f(j - 1)<j$.
- If $T[i] \neq P[j]$ and $j = 0$, then $i$ increases by 1, $j$ does not change, and $k$ increases by 1.

Therefore at each iteration of the loop either $i$ or $k$ increases by at least 1 (and potentially both). As the algorithm terminates when $i$ reaches $n$ (end of text, no match found), the worst case is that $i$ reaches $n$ from only the first case and $k$ reaches $n$ by only the second case, with increases of 1. Therefore, the number of iterations is at worst $2n$. This is still assuming we have already computed the failure function for $P$.

### Constructing the Failure Function
Essentially, perform KMP on the pattern against itself but for each match record how far ahead the suffix is from the prefix ($j + 1$).
```python
algorithm KMPFailureFunction(str P):
	i = 0
	j = 0
	Func f(0) = 0  # this is really more of a map than a function
	while i < P.length:
		if P[j] == P[i]:  # pattern has matched itself
			f(i) = j + 1  # j + 1 characters from the prefix matched here
			i++
			j++
		else if j > 0:  # no match, but we did match a bit of the start
			j = f(j - 1)  # we can skip this many comparisons
		else:  # no match here
			f(i) = 0
			i++
	return "No substring of T matching P"
```
Note how the failure function uses previous values of itself to increase efficiency.
Considering the algorithm for finding the failure function is essentially identical to KMP, it comes as no surprise that it also runs in $O(n)$-time.

Therefore, the overall complexity of KMP is $O(n + m)$.

It is most efficient for small alphabets, due to the higher chance of repeated characters and substrings. An example of such an alphabet is DNA sequences.