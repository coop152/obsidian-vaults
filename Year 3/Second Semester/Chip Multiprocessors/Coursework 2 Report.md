# Question 1
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

# Question 2
The program is likely to deadlock at the beginning of `Philosopher::eat()`, where the Philosopher tries to get the locks on the two forks. 
This is because after one philosopher takes their left fork, there is a time period where another philosopher can take their right fork, forcing them to wait. If this happens in a chain such that they are waiting on a philosopher that is waiting on them, they will wait forever. For example, if there are two philosophers:
- Phil. 1 takes their left fork (Phil. 2's right fork)
- Phil. 2 takes their left fork (Phil. 2's right fork)
- Phil. 2 tries to take their right fork (Phil. 1's left fork), fails and waits
- Phil. 2 tries to take their right fork (Phil. 1's left fork), fails and waits

# Question 3
The fine-grain solutions are more performant than the coarse grain solutions, however 