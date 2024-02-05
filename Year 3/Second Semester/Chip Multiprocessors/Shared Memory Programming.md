**Threads** - Each thread runs on a CPU core, independently from the others. Every thread associated with a process **shares the same address space**.

# Threads in C with Pthread
Use `pthread_create()` to create and launch a thread. Parameters:
- Function pointer to the function that should be run
- Optionally, parameters to that function

Call `pthread_exit()` to exit the current thread.
Call `pthread_join()` to wait for another thread to finish.


# Data parallelism
A simple form of parallelism, where you divide a pool of work into (ideally) equal sized chunks and process them in parallel. This works best when there are no data dependencies between chunks.
For example, matrix multiplication is well suited for parallelisation with this method.
![](Pasted%20image%2020240205103201.png)
Each cell of the resulting matrix is independent from the other cells, so they can be calculated in parallel with no data dependencies.
You could calculate every single cell at once, which requires lots of threads:
![](Pasted%20image%2020240205103256.png)
You could have a thread for each row:
![](Pasted%20image%2020240205103338.png)
You could allocate some number of cells to each thread:
![](Pasted%20image%2020240205103439.png)