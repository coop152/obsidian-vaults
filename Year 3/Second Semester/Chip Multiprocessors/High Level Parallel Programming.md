![](Pasted%20image%2020240325144849.png)
This section will cover various higher level methods of parallelising a program, going from lowest to highest level.
# Low Level Standard Parallelism (`std::threads`)
Previously we have used the C pthreads library to parallelise our code. This works, but thanks to being a C library it is:
- Extremely verbose and tedious to write, thanks to the lack of constructors and destructors requiring a bunch of init and destroy calls on everything
- Error-prone, thanks to the lack of generic types leading to the use of void pointers, which prevents type-checking
- Hard to read, because everything has a triple-barrelled overlong name like `pthread_mutex_unlock` thanks to the lack of classes and namespaces

Before we move on to the more distinct parallel programming methods, we investigate the C++ `std::threads` library, which acts as a wrapper around pthreads and remedies these issues.

The C++ threads library is still **low level**, or in other words **imperative**. We are telling the hardware/OS exactly what to do (e.g. make a thread, lock a variable, place a barrier), and not what we intend to do (e.g. run some algorithm in parallel), which inherently limits both our ability to easily read the code, and the compiler's ability to optimise it. It is essentially syntactic sugar around pthreads.
Most languages will have a similar library.

## `pthread` vs `std::thread`
![](Pasted%20image%2020240325145202.png)
Creating threads is similar in pthreads and std::threads, but is obviously more clean. Notably:
- There is a lot less boilerplate (no passing around a thread struct, no NULL arguments, declaration and initialisation as one)
- All the names are just shorter (e.g. `pthread_join` to `var.join()`)
- No void pointers, so everything is type-checked and the meaning of the involved functions is much more obvious

## `pthread_mutex` vs `std::mutex`
![](Pasted%20image%2020240325145529.png)
We get similar benefits as with `std::thread` by directly translating the C code to C++, with the same readability and conciseness. However, we can go one better and use `std::lock_guard`, which makes locking and unlocking foolproof. Instead of having to manually unlock when we're done with a resource, it is **automatically** unlocked when the lock guard goes out of scope.
While this doesn't give much improvement in simple examples, this is a boon in more complicated examples with multiple exit points (e.g. try-catch clauses that exit early, multiple normal paths that end in a return). Using the normal lock/unlock, it is very easy to forget to unlock on all paths, which leads to hard-to-debug situations where the program seemingly hangs for no reason as the thread holds the resource forever. A lock guard will always release the lock, saving you the trouble.

## `std::threads` Locks
In addition to the previously mentioned lock guard, `std::threads` offers a few locks:
- `std::lock_guard` - Lock at creation, unlock at destruction
- `std::scoped_lock` - Lock **multiple** at creation, unlock at destruction (has built-in deadlock avoidance!)
- `std::unique`