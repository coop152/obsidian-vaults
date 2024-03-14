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
The coarse-grain solutions incur a performance penalty whenever two threads try to pick up **any forks at all** at the same time. The fine-grain solutions incur no penalty in this situation, and only incur a performance penalty when **the same fork** is being picked up by two threads. For low thread counts this doesn't amount to much difference, because there are less forks and therefore the chance of two threads picking up the same fork is proportionally higher (which explains observation 1). For high thread counts the chance of two threads picking up different forks is higher, so this fine-grain-advantageous situation will happen more often, making the coarse-grain solutions accumulate more overhead and thus perform worse (which explains observation 2).  

Because contention over a specific fork is so much less likely, there will be fewer threads going to sleep over the course of the program's runtime with the fine-grain solution. Therefore, there is less chance for the small speed difference between the mutex and spinlock to accumulate and the overall difference is minor. In contrast, the locking occurs so frequently in the coarse solution that the small speed differences are able to add up to a large difference.