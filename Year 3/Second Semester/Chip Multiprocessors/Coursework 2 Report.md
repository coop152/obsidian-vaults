### Question 1
The program can enter an invalid state in `Fork::take()` if two philosophers try to take a fork at the same time. It is possible for a second philosopher to check `_free` and get past the check before the first philosopher has changed it to false. In this case they both believe they have taken the fork, but only one will be assigned as the actual holder according to `fork._holder`. For example:

| Philosopher 1           | Philosopher 2           |
| ----------------------- | ----------------------- |
| `if(!_free) return -1;` |                         |
|                         | `if(!_free) return -1;` |
| `_free = false;`        |                         |
| `_holder = p;`          |                         |
|                         | `_free = false;`        |
|                         | `_holder = p;`          |
| `return 0;`             |                         |
|                         | `return 0;`             |
### Question 2
The program is likely to deadlock at the beginning of `Philosopher::eat()`, where the Philosopher tries to get the locks on the two forks. 
This is because after one philosopher takes their left fork, there is a time period where another philosopher can take their right fork, forcing them to wait. If this happens such that they are waiting on a philosopher that is waiting on them, they will wait forever. For example, with two philosophers:
- Phil. 1 takes their left fork (Phil. 2's right fork)
- Phil. 2 takes their left fork (Phil. 2's right fork)
- Phil. 2 tries to take their right fork (Phil. 1's left fork), fails and waits
- Phil. 2 tries to take their right fork (Phil. 1's left fork), fails and waits
### Question 3
1. All of the solutions demonstrate similar performance at lower thread counts. 
2. As the number of threads increases the coarse-grain solutions experience a performance falloff, while the fine-grain solutions stay almost linear in growth. 
3. The fine-grain mutex and spinlock solutions perform very similarly, but the coarse-grain spinlock solution is significantly faster than the mutex version.
### Question 4
1. When the thread count is low the chance of a race condition is also low, causing very few threads to wait (and thus incur overhead) in the fine-grain solution. The threads will have to wait more often in the coarse-grain solution, but they won't have to wait for long because the low number of threads limits the length of any chain of waiting threads.
2. The locking in the fine-grain solutions only incurs a performance penalty when there is contention over a specific fork (pthread's mutex is implemented with a futex), which is relatively unlikely even with many threads. In contrast, the locking in the coarse-grain solution incurs a performance penalty every time a thread tries to get **any** fork at the same time as another, which is much more likely to happen and will also happen much more frequently as the number of threads increases.
3. Because contention over a specific fork is so much less likely, there will be fewer threads going to sleep over the course of the program's runtime with the fine-grain solution. Therefore, there is less chance for the small speed difference between the mutex and spinlock to accumulate and the overall difference is minor. In contrast, the locking occurs so frequently in the coarse solution that the small speed differences are able to add up to a large difference.