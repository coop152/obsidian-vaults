# Expected speedup
I predict that the greatest speedup will be achieved using a number of threads equal to the available hardware threads (i.e. 48 threads on mcore48.) 
I expect that the multithreaded performance will scale roughly in-proportion to the number of threads, up until the physical hardware thread count is reached wherein it will gradually decrease. 
In my graph i represent the first part as a linear 
## First attempt
On the first attempt, I implemented the sum operation