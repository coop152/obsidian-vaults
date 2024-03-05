This section is concerned with how the operating system exposes threads to applications and maps this to hardware.
![](Pasted%20image%2020240305133640.png)
The specific example investigated will be the Linux kernel.
# Thread Management
A thread is a unique schedulable entity in the system, and each process has at least 1 of them:
![](Pasted%20image%2020240305133742.png)
In Linux, processes are identified by their unique PID (process identifier). You might use the command `ps -A` to list the PIDs of the programs running on a Linux machine. Similarly, threads are identified by a unique TID (thread identifier). Threads that share address space will also share a PID. You can list all of the threads running on a Linux machine using the command `ps -AT`. 

A C program can access the PID and TID of its process/thread using the `getppid` and `gettid` functions:
```c
#define _GNU_SOURCE  // Required for gettid() on Linux
/* includes here */
void *thread_function(void *arg) {
    printf("child thread, pid: %d, tid: %d\n", getpid(), gettid());
    pthread_exit(NULL);
}

int main() {
    pthread_t thread1, thread2;
    printf("parent thread, pid: %d, tid: %d\n", getpid(), gettid());

    pthread_create(&thread1, NULL, threadFunction, NULL);
    pthread_create(&thread2, NULL, threadFunction, NULL);
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);
}
```
This will give output similar to this:
```bash
parent thread, pid: 12674, tid: 12674
child thread, pid: 12674, tid: 12675
child thread, pid: 12674, tid: 12676
```
Notice that each thread reports the same PID, but a different TID. The parent thread's TID is also equal to its PID, indicating it is that process' original thread.

# Creating Threads
## The syscall
Programs create processes and threads using the `clone` system call. It has this signature:
```c
long clone(unsigned long flags, void *stack, int *parent_tid, int *child_tid,
        unsigned long tls);
```
When using it to make a process, most of these parameters can be ignored and set to 0/NULL. In this case it acts similarly to `fork`; all of the parent process' resources are duplicated for the child process, including its address space.
When we use it to create a new **thread**, which shares the parent's address space, the parameters are important:
- `flags` is where we specify the thread creation options
- `stack` is a pointer to the space in memory which will be used as a stack for the new thread
- `tls` is a pointer to the space in memory which will be used as **thread local storage** for the new thread
- `parent_tid` and `child_tid` are pointers to variables where the corresponding TIDs will be stored.

It is obvious why each thread needs its own stack, but what is the **thread local storage**? This area of memory holds a special type of global variables that have **per-thread values**. If we want to define such a variable, we prefix the declaration with the `__thread` keyword. This is important for things such as `errno`, which has to be global but must also have a different value for each thread.

## How pthreads uses it
Let's investigate how `pthread_create` utilises the `clone` syscall. We investigate the Musl C stdlib, because Glibc is convoluted as fuck.
Musl libc's `pthread_create` implementation performs the following (simplified) steps:
1. Sets up the flags with `CLONE_VM | CLONE_THREAD`, among other things. These two flags are the most important ones, as they indicate that we want a thread as opposed to a process, and that we don't want to make a copy of the parent's address space.
2. Allocates space for the new thread's stack using `mmap` (which is the syscall that asks for memory from the kernel, used by malloc in C or new in C++ under the hood.) It also creates a TLS area using `__copy_tls`, which is pointed to by the variable `new`.
3. Puts some data onto the newly created stack. Specifically, it places the code entry point for the function that the new thread will execute, along with the arguments that it will take.
4. Calls the `clone` wrapper function `__clone`:
```c
ret = __clone(start, stack, flags, args, &new->tid, TP_ADJ(new), &__thread_list_lock);
```
This wrapper function will perform the `clone` syscall for you. `clone`'s implementation is quite architecture specific, so it is written in assembly. Here is the x86_64 implementation:
```asm
__clone:
	xor %eax,%eax     // clear eax
	mov $56,%al       // clone's syscall id in eax
	mov %rdi,%r11     // entry point in r11
	mov %rdx,%rdi     // flags in rdi
	mov %r8,%rdx      // parent_tid in rdx
	mov %r9,%r8       // TLS in r8
	mov 8(%rsp),%r10
	mov %r11,%r9      // entry point in r9
	and $-16,%rsi
	sub $8,%rsi       // stack in rsi
	mov %rcx,(%rsi)   // push thread args
	syscall           // actual call to clone
	test %eax,%eax    // check parent/child
	jnz 1f            // parent jump
	xor %ebp,%ebp     // child clears base pointer
	pop %rdi          // thread args in rdi
	call *%r9         // jump to entry point
	mov %eax,%edi     // ain't supposed to return
	xor %eax,%eax     // here, something's wrong
	mov $60,%al
	syscall           // exit (60 is exit's id)
	hlt
1:  ret               // parent returns
```
By consulting the calling convention (System V ABI, in Linux's case) we can determine which of these registers is which argument:

|Argument number|x86-64 register|
|---|---|
|1|`%rdi`|
|2|`%rsi`|
|3|`%rdx`|
|4|`%rcx`|
|5|`%r8`|
|6|`%r9`|
Any arguments after these are passed on the stack