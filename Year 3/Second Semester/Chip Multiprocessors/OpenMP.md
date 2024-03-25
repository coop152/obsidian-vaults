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

# Reduction Clause
The previous result is okay, but we still had to modify the code a little and the performance increase is not as high as it could be. Specifically, the serial section where we sum up the results into `answer` is a bottleneck.
In this case, there is a solution to eliminating this final sum; the `reduction()` clause. This clause can be used to update some variable with the result of each iteration (e.g. sum, product, min, max). As long as the reduction operation is **associative** (i.e. the order of updating the result variable doesn't matter) we can use this clause to eliminate that serial bottleneck.
To use this clause, we can undo the changes we made to facilitate that serial summation and add the clause to the directive:
![](Pasted%20image%2020240325190624.png)
The clause modifies how OpenMP handles the reduction variable in these ways:
- When entering the loop, each thread gets a private/local copy of the variable (instead of having a single shared copy). These take a value determined by the operator in the clause; addition or subtraction get 0, multiplication gets 1, etc.
- When in the loop, each thread updates its own copy.
- When exiting the loop, the local results are combined with the operator specified in the clause.

You can use the reduction clause to output to simple variables as shown previously, but you can also output to arrays by providing an output range, like so: `reduction(op:arr[start_offset:end_offset])`.
You can even reduce to a user defined type by defining your own reduction operator.

# Variable visibility
Note again that the previously shown code is **not correct**: By removing our summation, we have reverted `res` to a single variable, and it is now shared between all threads again. 
To be more specific, OpenMP uses these rules to decide the visibility of variables in the threads by default:
- If a variable is declared outside of the parallel region, it is **shared between all threads** (With one exception in the loop counter.)
- If a variable is declared inside the parallel region, it is **private to each thread**, and they each get their own *uninitialised* copy.

We want `res` to be private to each thread, so we could declare it inside the parallel region. Alternatively, we could use one of the **data sharing clauses** provided by OpenMP:
- `shared()`, which marks a variable as shared in the parallel block,
- `private()`, which marks a variable as private in the parallel block *and doesn't initialise it*,
- `firstprivate()`, which marks a variable as private in the parallel block *and initialises it to the value of the original variable*,
- `lastprivate()`, which marks a variable as private in the parallel block *and updates the original value at the end*.

In this case we can simply use `private()`.
![](Pasted%20image%2020240325192323.png)
And our solution is now completely correct. We have made zero changes to the original code apart from adding the annotation, and it is more performant than the naive solution thanks to the elimination of the serial sum.
(Realistically we would never have had a res variable in the first place.)
![](Pasted%20image%2020240325192506.png)

# Synchronisation
The previous example was **embarrassingly parallel**; most code is not so easily and cleanly parallelised. OpenMP provides constructs for coordinating between threads in these less-than-optimal situations:
- `barrier`, to make all threads wait. (There is an implicit barrier at the end of all work sharing constructs)
- `nowait`, to skip the implicit barrier at the end of work sharing constructs.
- `omp_set_lock` and `omp_unset_lock`, to create locks manually (these are functions in the runtime library, not pragmas)
- `critical`, to define a critical section where only 1 thread will run at a time
- `atomic`, to make memory updates in the following statement atomic

For example, you could also solve the race condition in the previous example using a critical section:
![](Pasted%20image%2020240325200551.png)
Or by making the update statement atomic:
![](Pasted%20image%2020240325200604.png)
Though both of these are worse than the reduction.

# Loop iteration assignment
When splitting a loop, OpenMP must decide which iterations of the loop to assign to which thread. The way that it assigns the iterations is decided by the **loop scheduling mode**. By default, it uses **Static Block Scheduling**:
![](Pasted%20image%2020240325200841.png)
This splits the iterations into contiguous chunks. You can set this scheduling mode using this pragma: `#pragma omp parallel for schedule`

Another mode is **Static Loop scheduling**:
![](Pasted%20image%2020240325201107.png)
This splits the iterations into non-contiguous stripes. You can modify the **chunk size**, which gives a pattern somewhere in the middle of block and loop scheduling:
![](Pasted%20image%2020240325201317.png)
Where each thread works on chunks of n iterations, before jumping to the next stripe like in loop static loop.

Both of these scheduling modes are **static**, which means that they decide which thread gets which iteration at the start and it stays fixed for the duration of the parallel block. This has very low overhead, and works well when every iteration takes about the same time to finish, but it has poor results when each iteration has a different runtime:
![](Pasted%20image%2020240325201710.png)
In this situation you should use **Dynamic loop scheduling**. 
![](Pasted%20image%2020240325201853.png)
In this mode, after a thread has processed a chunk of iterations (1 in the example), it asks the scheduler for another chunk. This balances the work out, and reduces the overall runtime in situations where one thread might run much longer than the others due to iterations running for a long time. However, it introduces extra overhead, which can degrade performance if the balancing provides little benefit.
Here is a (contrived) example of where dynamic scheduling can be useful:
![](Pasted%20image%2020240325202031.png)

# Thread count
OpenMP also allows you to set the number of threads manually. It defaults to the number of cores on your system, but you can change it three ways:
![](Pasted%20image%2020240325202149.png)
There usually isn't a good reason to change this, other than for testing.

# Beyond loops
Loop splitting is a great, because loops are easy to parallelise, and their fixed size allows low runtime overhead. But only using loop splitting is limiting; you don't always have a predetermined, fixed number of iterations to split up. OpenMP provides options beyond loop splitting to allow more types of code to be parallelised:
- sections, which parallelise "straight line" code
- tasks, which allow asynchronous computation
- singles, which are executed by only one thread (see the use for this later)
- masters, which are executed by only the main thread

## Sections
Consider this code:
![](Pasted%20image%2020240325202916.png)
We have two implementations of the fibonacci sequence, one loop based and one recursive. The loop based implementation cannot be parallelised with loop splitting because it has dependencies, and the recursive implementation has no loop to split.
However, there is potential for parallelism in the main function. The calls to fib1 and fib2 are both independent, and can run simultaneously. Sections allow us to parallelise code such as this.
![](Pasted%20image%2020240325203204.png)
To execute arbitrary code blocks in parallel, we surround all of the blocks in a `sections` pragma, and then annotate the individual sections with a `section` pragma each. The sections will be run on different threads in parallel, and then when they are all complete, execution will progress beyond the `sections` block.

## Tasks
Consider this code:
![](Pasted%20image%2020240325203805.png)
The main operation in this code (the call to fib2) is parallelisable with any other call of fib2; it is fine to run multiple iterations of this loop at a time. However, we cannot run iterations of the whole loop in parallel because there is a dependency between the whole iterations; the value of `it` is updated after fib2 is calculated.
With tasks, instead of running the entire loop body in parallel, we defer execution of the long-running fib2 call by putting it into a queue of tasks. Otherwise, the loop runs serially. This allows us to get the correct value of `it` for each call to fib2, but at the same time it populates the queue with every single call to fib2. The scheduler can then easily pick multiple calls from the queue and run them in parallel.
To do this, we use `task`:
![](Pasted%20image%2020240325204211.png)
Be careful; this code is incorrect. Because of the `parallel` block, the while loop will be run multiple times on many threads, which was not the desired behaviour. We only want the loop to be run on one thread, and the rest of the threads should be handling the tasks from the queue. We can indicate this using `single`:
![](Pasted%20image%2020240325204452.png)There is one last problem with this code, and it regards the visibility of the `it` variable. As per the default rule, `it` is a shared variable across all threads. This is not what we want; right now, when a task is taken from the queue it will read the current value of `it`, but we want the task to execute using the value of `it` from when it was put into the queue. To remedy this we use `firstprivate`:
![](Pasted%20image%2020240325204728.png)
This makes `it` local to the line of code which the task executes, and sets the value of the local version equal to the value of the version from outside. This gives the desired result.
Tasks are the least restrictive of all the parallelism constructs in OpenMP, and we can take this example even further. The recursive definition of `fib2` has two parallelisable calls:
![](Pasted%20image%2020240325204959.png)
Note that this example only works in the greater context of the previously shown main function. If there wasn't a parallel block around the initial fib2 calls then the annotations here would do nothing.
This is also a **bad example** of using tasks. Tasks are the most flexible of the parallelism constructs, but also have the **highest overhead**, and this example could make millions of tasks, each with very little work. Tasks should be used sparingly, in situations where the code that can be parallelised is sufficiently long-running.
We can conditionally create tasks, if we encounter a situation where tasks are only optimal in certain cases. For example, we could try and reduce the maximum number of tasks created in fib2 like so:
![](Pasted%20image%2020240325205524.png)
Whether this would be effective is another question.

### Dependencies
`taskwait` is quite simple in its operation, but OpenMP supports more complex task patterns. The `depend` clause takes a list of variables that are input or output dependencies of a task, and if a task has an input dependency that another task has as an output, the scheduler will make that task wait until an output has been generated by the other task. This allows us to construct complicated task graphs. For example:
![](Pasted%20image%2020240325205831.png)
In this case the `update_with_data` task depends on the output of the `preprocess` task, so it will be left in the queue until the `preprocess` task is complete. The `postprocess` task, which has no dependencies, can be run at any time.