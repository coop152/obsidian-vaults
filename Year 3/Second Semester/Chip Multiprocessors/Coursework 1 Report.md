# Expected speedup
I predict that the greatest speedup will be achieved using a number of threads equal to the available hardware threads (i.e. 48 threads on mcore48), where performance will reach 12 times single-threaded.
I expect that the multi-threaded performance will scale roughly in-proportion to the number of threads, up until the physical hardware thread count is reached wherein it will gradually decrease. 
I represent this as an exponential curve up to point 
## First attempt
On the first attempt, I implemented the vecadd straightforwardly, but the performance results didn't fit my expectations. On my local machine performance did peak at the hardware thread count (4), but it also peaked there on mcore48. The speedup was also very small at around 1.5x single-threaded performance, and it was slower than single-threaded for most other thread counts.

(graph of shitty version goes here)

I believed this was a memory bandwidth bottleneck, so I added some useless arithmetic operations to artificially make the problem less memory-intensive.  I also increased the vector size from 1,000,000 to 10,000,000.
## Second attempt
With the less memory-intensive version, the results were mostly as expected. The performance now scales up to the hardware thread count (4 on my local machine, 48 on mcore48) and then gradually degrades as the thread count becomes too high. The final performance improvement is still not as large as I expected, with performance hitting around 6x of singlethreaded at the peak.
![](chart.svg)
At this point I tried different vector sizes