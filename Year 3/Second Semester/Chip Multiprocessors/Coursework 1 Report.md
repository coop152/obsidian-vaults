# Expected speedup
I predict that the greatest speedup will be achieved using a number of threads equal to the available hardware threads (i.e. 48 threads on mcore48), and that the performance at that thread count will not reach the ideal 48x speedup.
I expect that the multi-threaded performance will scale roughly in-proportion to the number of threads, though it will suffer from a continually increasing overhead from the large number of threads. Therefore, once it exceeds the number of hardware threads it will start to decline in performance.
I represent this on the graph with a logarithmic curve from 0 to 48 cores - the decline afterwards is omitted because I don't have any particular idea for how fast it will decline. For the curve, I predict a 12x speedup at 48 cores.
## First attempt
On the first attempt, I implemented the vecadd straightforwardly, but the performance results didn't fit my expectations. On my local machine performance did peak at the hardware thread count (4), but it also peaked there on mcore48. The speedup was also very small at around 1.5x single-threaded performance, and it was slower than single-threaded for most other thread counts.

(graph of shitty version goes here)

I believed this was a memory bandwidth bottleneck, so I added some useless arithmetic operations to artificially make the problem less memory-intensive.  I also increased the vector size from 1,000,000 to 10,000,000.
## Second attempt
With the less memory-intensive version, the results were closer to expected. The performance now scales up to the hardware thread count (4 on my local machine, 48 on mcore48) and then gradually degrades as the thread count becomes too high. However, the final performance improvement is still not as large as I expected, with performance hitting around 6x of singlethreaded at the peak.
![](chart(1).svg)
At this point I tried different vector sizes and adding more artificial work to alleviate pressure on memory, but the results did not change significantly. I believe that the degraded performance may be due to caching: Because my program splits the vector into contiguous chunks, a thread will never be working on an element in the same cache line as another thread. In addition to the large vector size (10,000,000 32 bit integers, taking ~40MB), this would likely cause lots of memory accesses