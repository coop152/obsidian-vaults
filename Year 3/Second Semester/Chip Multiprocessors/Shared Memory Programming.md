**Threads** - Each thread runs on a CPU core, independently from the others. Every thread associated with a process **shares the same address space**.

# Threads in C with Pthread
Use `pthread_create()` to create and launch a thread. Parameters:
- Function pointer to the function that should be run
- Optionally, parameters to that function

Call `pthread_exit()` to exit the current thread.
Call `pthread_join()` to wait for another thread to finish.