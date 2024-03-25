OpenMP is a C++ library that allows automatic parallelisation of multiple common patterns, for example fork-join, parallel-for, and parallel-for-reduce.
It bears resemblance to an algorithmic skeleton system, but it lacks the full composability and global optimisation of one. Regardless, it still provides very high level abstraction and high performance, while minimally disrupting the imperative code that is being parallelised.

The goal of OpenMP is to allow programmers to get parallel performance in mostly serial code with minimal effort. This is achieved with a series of compiler directives and some functions, which modify regular serial constructs to branch work off into multiple threads.

# Fork-join
![](Pasted%20image%2020240325171658.png)
The most fundamental way that OpenMP achieves parallelism is via the Fork-join model.
The program has a primary thread which essentially follows the path that the program would follow if it were not using OpenMP at all. When parallel regions are entered, execution will fork into many threads which all execute the same code, then these threads are joined and the work collated. The program them proceeds in a single-threaded fashion until the next parallel region.

In C++, you indicate a parallel region by annotating a block with the **parallel directive**:
![](Pasted%20image%2020240325172024.png)
Code inside the block is run in parallel by multiple threads, and the program returns to a single thread when exiting the block.

This is the minimum possible program that demonstrates how OpenMP can be used:
![](Pasted%20image%2020240325172251.png)
(For this code to compile you need to use the `-fopenmp` flag in gcc.)
This will simply output "Hello World!" a number of times equal to the core count of your device.
Now, this isn't very useful because each thread is indistinguishable. To make a more useful minimum viable program, we can use OpenMP's runtime interface to get each thread's id:
![](Pasted%20image%2020240325172534.png)
In a more realistic example, you would use these thread ids to divvy up some work across these threads (for example, maybe you would index into an array using these id's to process it in parallel.)

This illustrates the baseline model of fork-join. It is general, but it isn't very structured and it doesn't really map onto existing serial code easily. OpenMP develops on this by providing specialised methods for the most common cases. Usually when parallelising a program we want to split some task up into pieces and then have the threads solve these pieces in parallel. Depending on how we want to split up our task, OpenMP offers two different approaches:
- Data parallelism ("Perform the same operation on different pieces of data"): Use loop splitting (parallel-for)
- Task parallelism ("Perform different operations at the same time"): Use sections and tasks (task-farm)

# Loop splitting
Loop splitting is useful for quickly and easily speeding up time consuming loops. Each iteration needs to be independent, but if they are then OpenMP can automatically assign iterations of the loop to different threads.

For example, take this code:
![](Pasted%20image%2020240325173455.png)
We have a loop which runs many iterations, where each one is independent and also long-running. This is an ideal candidate for parallelisation via loop splitting. OpenMP makes it simple enough:
![](Pasted%20image%2020240325173634.png)
First define the parallel region, which splits the program onto multiple threads, then mark the for loop with the `for` directive, which modifies the loop to do a different subset of the iterations on each thread.
You are also allowed to **merge** regions if they entirely overlap, which makes the previous code a good bit cleaner:
![](Pasted%20image%2020240325173820.png)
Finally we should notice that this implementation is incorrect; the threads are not independent, thanks to them all writing to the same variables `answer` and `res`. To remedy this we calculate answer outside of the loop on a single thread, which requires us to store an array of results (which incidentally solves our problem of `res` being shared).
![](Pasted%20image%2020240325183939.png)

We can see that making this code multithreaded using OpenMP required quite minimal changes. This code will also run correctly even if you don't use OpenMP; just pretend the pragma isn't there and see that the code is logically sound when run serially.

## Reduction Clause
The previous result is okay, but we still had to modify the code a little and the performance increase is not as high as it could be. Specifically, the serial section where we sum up the results into `answer` is a bottleneck.