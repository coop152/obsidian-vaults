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