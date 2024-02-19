# Expected speedup
I predict that the greatest speedup will be achieved using a number of threads equal to the available hardware threads (i.e. 48 threads on mcore48.) 
I expect that the multithreaded performance will scale roughly in-proportion to the number of threads, up until the physical hardware thread count is reached wherein it will gradually decrease. 
In my graph i plot the first part as a logarithmic curve, 
## First attempt
On the first attempt, I implemented the vecadd straightforwardly, but the performance results didn't fit my expectations: On my local machine performance did peak at the hardware thread count (4), but it also peaked there on mcore48. The speedup was also very small at around 1.5x performance