There ia a problem (PATH) that is known to be NLOGSPACE but isn't known to be in LOGSPACE. While we believe that it isn't in LOGSPACE, we don't know how to prove it. In fact, we don't know of any problem in NLOGSPACE that can be proved to be outside LOGSPACE. This question, whether LOGSPACE = NLOGSPACE, is analogous to the question of N = NP.
As a step toward resolving this L vs NL question, we can show some languages that are NL-complete. As with NP-complete languages, they are in some sense the most difficult languages in NL. If L and NL are different then we know that all NL-complete languages are not in L.
Like with NP-completeness, we define an NL-complete language as a language that is NL and to which every other language in NL is reducible. However, we don't use polynomial time reducibility here because **all problems in NL are solvable in polynomial time**. Therefore, every two problems in NL (except $\emptyset$ and $\Sigma^*$) are polynomial time reducible to each other. We use a new type of reducibility called **log space reducibility**.
![](Pasted%20image%2020231117122523.png)
![](Pasted%20image%2020231117122603.png)
If a language is log space reducible to a language already known to be in L then it is also in L, similarly to with P/NP.
![](Pasted%20image%2020231117122731.png)

## Proof example
