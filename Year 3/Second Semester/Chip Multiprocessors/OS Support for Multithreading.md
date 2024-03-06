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
## The clone syscall
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

## How pthreads uses clone
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

| Argument number | x86-64 register | `clone` argument | `__clone` argument    |
| --------------- | --------------- | ---------------- | --------------------- |
| 1               | `%rdi`          | flags            | start                 |
| 2               | `%rsi`          | stack            | stack                 |
| 3               | `%rdx`          | \*parent_tid     | flags                 |
| 4               | `%rcx`          | \*child_tid      | args                  |
| 5               | `%r8`           | tls              | `&new->tid`           |
| 6               | `%r9`           |                  | `TP_ADJ(new)`         |
| 7               | (in the stack)  |                  | `&__thread_list_lock` |
Any arguments after these are passed on the stack, and the return value is held in `%rax`.

Both the parent and child return from `clone` concurrently, leaving two threads executing `__clone`. If `clone` returned 0 then we are in the child, and if it returned a positive integer then we are in the parent. `__clone` checks this return value, and if it's the parent thread then it returns to the calling code. The child thread jumps to the given entry point and executes from there.
## How the kernel implements clone
When the `syscall` instruction is used in `__clone` to invoke the `clone` syscall, execution is handed over to the kernel. The kernel looks in `%rax` and sees the syscall id for `clone`, then calls a chain of functions (`sys_clone` -> `kernel_clone` -> `copy_process`) ending in `copy_process`. This function implements the duplication of the parent, calling various functions that check the given flags to decide what needs to be copied and what needs to be shared. For example, this (simplified) function handles the address space:
```c
static int copy_mm(unsigned long clone_flags, struct task_struct *tsk) {
  /* ... */
    if (clone_flags & CLONE_VM) {
        mmget(oldmm);
        mm = oldmm;
    } else
        mm = dup_mm(tsk, current->mm);
  /* ... */
}
```
If the flags have `CLONE_VM` set, then the structure `mm` (which represents the address space) is simply reused, causing the address space to be shared. If it isn't, for example when creating a new process, `dup_mm` is called to duplicate it. 
## What happens after clone is called
When we return to user space in the child thread, we jump to this Musl function:
```c
static int start(void *p) {
  struct start_args *args = p;
  /* ... */
    __pthread_exit(args->start_func(args->start_arg));
}
```
This sets up the arguments and runs the desired function, exiting the thread when it returns.
# Locks in the kernel
Under the hood, lock access primitives such as `pthread_mutex_lock` are implemented in the kernel. This is for good reason; the kernel is the entity with the ability to put threads to sleep and wake them up.
![](Pasted%20image%2020240305145531.png)
Historically, each lock operation (even take and release) required a system call, for example with System V semaphores. However, these user/kernel world switches are **expensive** and the overhead is not negligible. It can seriously impact performance, especially in scenarios where a lock isn't contended:
![](Pasted%20image%2020240305145838.png)
## Futex
The **futex** (Fast User space muTEX) is a low-level synchronisation primitive which takes advantage of the fact that we only need threads to sleep (and therefore only need the kernel to get involved) when a lock is contended. it can be used to build locks that are accessed partially in userspace using atomic operations when there is no contention. There is another part of the lock in kernel space, which is used when there is contention and threads need to be put to sleep.
A futex relies on a 32-bit variable in user space, which is accessed by threads trying to take/release the lock using atomic operations. When it is equal to zero, the lock is free:
![](Pasted%20image%2020240305150541.png)
In this state, a thread that wants to take the lock can try to do so with an atomic CAS. If it succeeds, the thread got the lock without needing to involve the kernel at all, and it can proceed with it's critical section:
![](Pasted%20image%2020240305150638.png)
If another thread tries to take the lock with another CAS, it will fail. In that case the thread needs   to go to sleep, so the OS kernel must be involved. This system call is named `futex`.
![](Pasted%20image%2020240305150753.png)
The kernel double-checks to see if the lock is still taken with a CAS of its own, and if it is then it puts the thread to sleep by putting it in the **wait queue**.
![](Pasted%20image%2020240305150923.png)
Other threads trying to take the lock will play out similarly:
![](Pasted%20image%2020240305150948.png)
When the thread with the lock wants to release it, it uses a(n atomic) store instruction to reset the user-space variable to zero. Vitally, it also makes a `futex` system call to alert the OS that one of the waiting threads can wake up.
![](Pasted%20image%2020240305151129.png)
The now-awake thread will try to take the lock with a CAS, and succeed:
![](Pasted%20image%2020240305151152.png)
And so on.
Note that in this simple example implementation, a thread releasing a lock will always have to make a syscall, even if there are no waiters. This is because there is no indication of how many waiters there are in user space.
![](Pasted%20image%2020240305151410.png)
An optimised implementation would remedy this by, for example, encoding the number of waiters in the 32-bit user land variable.
### Implementation
Here is a simple lock implementation using `futex`.
```c
atomic_int my_mutex = ATOMIC_VAR_INIT(0);

int my_mutex_lock() {
    int is_free = 0, taken = 1;

    // cas(value_to_test, expected_value, new_value_to_set)
    while(!atomic_compare_exchange_strong(&my_mutex, &is_free, taken)) {
        // put the thread to sleep waiting for FUTEX_WAKE if my_mutex is still equal to 1
        syscall(SYS_futex, &my_mutex, FUTEX_WAIT, 1, NULL, NULL, 0);
    }
    return 0;
}

int my_mutex_unlock() {
    atomic_store(&my_mutex, 0);

    // wake up 1 thread if needed
    syscall(SYS_futex, &my_mutex, FUTEX_WAKE, 1, NULL, NULL, 0);
    return 0;
}
```
We have our user space variable `my_mutex`, which is initialised to 0 using the C atomic operations API.
The lock taking function `my_mutex_lock` performs a CAS using `atomic_compare_exchange_strong`, checking if the lock is 0 (i.e. if it's free). If it is free, the CAS stores 1 to the variable and returns 1, causing `my_mutex_lock` to return. If not, the `futex` syscall is called, taking the lock variable and `FUTEX_WAIT` flag among other arguments. This prompts the kernel to put the calling thread in the wait queue until the given lock variable is free. When that thread awakens, it will attempt another iteration of the loop, most likely getting the lock.

The `mutex_unlock` operation uses an atomic store to set the user space variable to 0, indicating it is free. it then calls `futex`, this time with the `FUTEX_WAKE` flag to indicate to the kernel that it can wake up `1` waiter (if there is one).
## Performance comparison
