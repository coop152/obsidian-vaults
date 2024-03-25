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
- `std::unique_lock/std::shared_lock` - SImilar to a lock guard, but capable of deferred locking, timed locking, etc.

## Atomic operations
![](Pasted%20image%2020240325150439.png)
In pthreads, there is no support at all for atomic operations; you just have to lock the variable every single time you do anything to it, which is inefficient and ugly. The common method to remedy this is to forgo the lock and use **built-in** atomic functions. This is definitely better than using a lock, but it is extremely platform dependent.
In `std::threads`, we can easily make every single operation on a variable atomic using `std::atomic`:
![](Pasted%20image%2020240325150728.png)
This adds some overhead, so if we don't always want to update a variable atomically we can use `std::atomic_ref`:
![](Pasted%20image%2020240325150940.png)
Now we can update the variable atomically by referencing it through the `std::atomic_ref`, and update it normally by referencing it directly.
Both of these make code less error-prone, significantly cleaner, and more portable compared to the two C methods.

## `pthread_barrier` vs `std::barrier`
![](Pasted%20image%2020240325151139.png)
Barriers (as well as the various other constructs such as condition variables, semaphores, latches, etc.) are also significantly simplified by `std::threads`. 
Barriers have a special common case wherein `std::threads` is actually **more performant** than pthreads. It is common that after you've synchronised your threads with a barrier, you want one thread to perform a "round-up" operation and the others to wait, then when that thread is done all threads continue. With `pthreads` the only way to do this is to add a second barrier, which has significant overhead. With a `std::barrier`, you can simply provide a callback function which will automatically be called on one thread when they all arrive:
![](Pasted%20image%2020240325151505.png)

## Summary
Low level standard parallel libraries such as `std::threads` are essentially syntactic sugar over the basic parallel operations. They are easier to understand and can avoid many pesky errors, but they still hit up against the same limitations in expression and optimisation as a low level API like `pthreads` does.

# High Level Standard Parallelism
C++ provides higher level parallelism support through the Standard Template Library. By using the same standard containers (e.g. `std::vector`, `std::array`) and standard algorithms (e.g. `std::transform`, `std::reduce`), the standard library can internally use parallel implementations of those algorithms to improve performance without requiring the user to write any parallel code themselves.
This is disabled by default; to enable parallelism in the STL we need to manually provide **execution policies** that tell it to enable parallelism. These execution policies are:
- `sequenced_policy` - Default, no parallelism
- `unsequenced_policy` - Single threaded, vectorised execution (SIMD)
- `parallel_policy` - Multi threaded execution, no vectorisation
- `parallel_unsequenced_policy` - Multi threaded, vectorised execution (SIMD)

## `std::threads` vs parallel STL
![](Pasted%20image%2020240325152304.png)
Here we enable parallel vectorised execution in `std::transform` by providing the `par_unseq` execution policy as the first argument. The benefits of this code are obvious:
- Much, much shorter
- Don't need to explicitly define the number of threads, which allows the program to decide depending on the device
- The meaning of the code is much clearer (specifically in the lambda function), where the meaning is buried in the `add_vec` function using `std::thread`)

We have more than 80 algorithms at our disposal in the STL that can be parallelised in this way. For example:
- all/any_of
- copy/fill
- find/search
- for_each
- generate
- min/max/minmax
- sort
- reduce
- remove
- transform

## Pros and Cons
Using the parallel STL:
- Conveys the high-level meaning of your code, as opposed to the low level implementation details
- Eliminates data races entirely (assuming the programmer implements their lambda functions correctly)
- Allows scaling to any size and type of device, thanks to the obfuscated implementation

However, a significant downside is that each operation is only parallel within itself. When the single operation completes it converges back onto a single thread, so a series of parallel STL operations will be continually bottlenecked, and will suffer from increased overhead.
*Technically*, the design could also allow for operations to be composed, using views:
![](Pasted%20image%2020240325153157.png)
Because views don't evaluate the result until the very end, after all of the algorithms have been composed, it is possible for some C++ STL implementation to perform every operation in parallel without bottlenecking between them. However, this is not actually true in any current implementation, and the parallel execution policies are not allowed for views.

Parallel STL also has limited support, only being standardised in C++17. MSVC supports it fully, while GCC only supports it in recent versions (9+) with the use of an Intel library (`-Itbb`) and clang doesn't support it at all. 
Nvidia also has an implementation of the parallel STL for CUDA C called Thrust, though it isn't really comparable to the others as CUDA C code runs on the GPU (and is generally just a different language).

## Functional-ish programming
Code utilising the STL algorithms often resembles functional code, where the program is defined by a sequence of transformations on data. It is no coincidence that both functional languages and the STL algorithms are so easily parallelisable; it is the declarative style that facilitates it.
Other languages include these functional-like programming interfaces:
- STL algorithms in C++
- Streams in Java
- map/reduce/filter/zip built-ins in Python, as well as list comprehensions in general, and generators, and the functools library... (Although none of these are actually parallelised.)

## `std::future`
C++ also provides an interface for asynchronous programming, similar to languages such as Javascript and Python. These interfaces allow computations to be offloaded and "put in the background", then for the result to be retrieved later when it is required. Some of the types provided are:
- `std::future` and `std::promise` - Objects that jointly represent the result of some asynchronous work. You request the result of the work with `std::future.get()`.
- `std::async` - Takes some function (along with arguments) and "runs it in the background", returning a `std::future` that can be used to get the result of that function. Depending on how it is called, may automatically create a thread pool and run the function on another thread, or it may defer the execution until the result is requested, etc.
- `std::packaged_task` - Similar to `std::async` in that it packages a function to run asynchronously, but it doesn't actually execute it immediately. The resulting `std::future` can be manually executed by requesting the result, or manually given to a thread in a thread pool, etc. Think of `std::async` as a convenient wrapper around `std::packaged_task` and `std::thread`.

# Beyond Standard C++
There is a limit to how high-level you can make the parallelisation of a general purpose programming language. Because the language is general, it is hard (or even impossible) for the compiler to analyse the code and parallelise it. Imperative languages are especially poor in this regard.
In a less technical sense, it is also difficult to add high level parallel interfaces to general purpose languages because of how long it takes to add new features to these languages. The process of implementing new features is slow by design, in order to prevent poorly implemented or disliked implementations from making it into the language. High level interfaces tend to be much harder to get in because of how much they deviate from the core language.
The following three approaches to higher-level parallelism avoid these problems in differing ways:
- Parallel libraries - Hide the parallelism from the user entirely, presenting an interface that performs very specific but performant operations
- Domain Specific Languages - Forgo the general purpose languages entirely (for parallel parts of the program) and use more limited (or more focused?) languages that can be parallelised more easily by the compiler
- Algorithmic Skeletons - Define parallel work pipelines in terms of skeleton operations which define how work is split (e.g. map, reduce, zip) and muscle operations which define what work is actually done. The way that functional-like algorithms are implemented in many general purpose programming languages is quite similar to this, which makes it easier to suggest implementing.

## Parallel Libraries
Parallel libraries allow the user to parallelise their code without concerning themselves with how the parallelism is implemented at all. Essentially, they just implement the code for you and then expose an easy interface.
They are by far the easiest way to parallelise your code, and require little to no knowledge of parallelism to use. Problems that are commonly parallelised with parallel libraries include:
- BLAS, Fast Fourier, LaPack 
- Sparse Algebra, Graph algorithms
- Deep Learning

The problem with parallel libraries is that they are very specific; if one exists for the problem you want to solve, then it's great, but there is a very high chance that one doesn't exist.
Parallel libraries are often implemented using low-level parallelism to maximise performance, meaning if one exists it is generally a good idea to use it over trying to implement it yourself (e.g. you would never implement your own neural network library in Python when PyTorch exists).

## Domain Specific Languages
