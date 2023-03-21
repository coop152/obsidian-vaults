1. 17.12% (1941 instructions) made up 90% of this program's execution time. I used the python script `ex1.py` to measure this, which sorts the instructions by number of executions in descending order and sums their execution counts until it exceeds 90% of the total, counting the number of instructions it took.
2. 428065 branches were taken.
3. 7.9986 instructions per branch.
4. 20% of execution time is wasted. The program takes 428065 branches, each of which wastes two cycles in addition to the cycle that it takes to execute. Therefore, the program runs for $\text{instructionCount} + 2\cdot\text{branchCount}$ cycles, which is 4280063 for the given trace. The proportion of wasted time is $\frac{2\cdot\text{branchCount}}{\text{totalTime}}$, which is almost exactly 20%.
5. 89.28% of the branches in the program are invariant.
6. 2.61% of execution time is wasted when invariant branches are perfectly predicted. When excluding invariant branches, only 45859 branches will incur a 2 cycle penalty to the runtime of the program. Doing the calculation from ex4 again, we get $\frac{2\cdot45859}{3515651}$ = 2.61%.
7. The BTB has a limited size and may have to evict some cached branches, which if they are used later will cause a delay. In general, it is unlikely that variant branches (e.g. jump tables or procedure return calls) will repeatedly go to the same location, so prediction is not likely to be perfect for them.
8. Approx. 43:24
9. Approx. 27:24
10. Forwards has a ratio of approx. 12:15, backwards has a ratio of approx. 15:60
11. Assume that all backwards branches will not be taken (i.e. regular fetching behaviour) and use the previous destination for forward branches (i.e. BTB)
12. 