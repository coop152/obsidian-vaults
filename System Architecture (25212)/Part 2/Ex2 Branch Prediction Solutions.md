1. 17.12% (1941 instructions) made up 90% of this program's execution time. I used the python script `ex1.py` to measure this, which sorts the instructions by number of executions in descending order and sums their execution counts until it exceeds 90% of the total, counting the number of instructions it took.
2. 428065 branches were taken.
3. 7.9986 instructions per branch.
4. 20% of execution time is wasted. The program takes 428065 branches, each of which wastes two cycles in addition to the cycle that it takes to execute normally. Therefore, the program runs for $\text{executedInstructionCount} + 2\cdot\text{branchCount}$ cycles, which is 4280063 for the given trace. The proportion of wasted time is $\frac{2\cdot\text{branchCount}}{\text{totalTime}}$, which is almost exactly 20%.
5. 89.28% of the branches in the program are invariant.
6. If every invariant branch could be predicted correctly, the BTB would only introduce a 2.68% overhead. When invariant branches are removed, the program has 45859 unpredicted branches which each cause 2 extra fetches. Overhead in this case is $\frac{\text{wastedFetches}}{\text{executedInstructionCount}}$.
7. The BTB has a limited size and may have to evict some cached branches, which if they are used later will cause a delay. In general, it is unlikely that variant branches (e.g. jump tables or procedure return calls) will repeatedly go to the same location, so prediction is not likely to be perfect for them.
8. Approx. 43:24
9. Approx. 27:24
10. Forwards has a ratio of approx. 12:15, backwards has a ratio of approx. 15:60
11. Assume that all backwards branches will not be taken (i.e. regular fetching behaviour) and use the previous destination for forward branches (i.e. BTB) ***{{this contradicts wikipedia, along with the ratios from the previous questions. try again}}***
12. Fully associative, as the small number of required entries means the O(n) lookup time will have a negligible impact on performance and thus it will likely perform better than a direct mapped cache.
13. Cache lines should be the length of one address, as the CPU only needs a single address from it at any one time.
14. See `model.py`.
15. Note that on this graph "overhead" is defined as $\frac{\text{wastedFetches}}{\text{executedInstructions}}$ (i.e. perfect branch prediction would have 0% overhead).![](Correlation%20between%20BTB%20Size%20and%20Execution%20Overhead.png)
16. The definition of overhead is the same as the previous graph.![](Correlation%20between%20BTB%20Size%20and%20Execution%20Overhead%20(1).png)
17. 59%
18. Newer processors may make use of "pattern history tables", which predict on patterns of repeated branches. For example, a pattern of branches like "100100100100" (where a branch is taken every third time) would behave poorly with a regular BTB or a two level BTB, but would be handled well by modern branch predictors. Some processors also incorporate special loop handling logic, with internal counters that correctly predict the entry and exit points of a loop, and special return handling logic, which remembers the address that a procedure was called from and predicts that it will be returned to.
19. 