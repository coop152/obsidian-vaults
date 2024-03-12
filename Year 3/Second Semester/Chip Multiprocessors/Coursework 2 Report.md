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
The program is likely to deadlock in `Philosopher::eat()`, where the Philosopher tries to get the locks on the two forks. 
```cpp
void Philosopher::eat() {
	...
	/* Take the fork on the left */
	while(_left_fork->take(this));
	...
	/* Now take the fork on the right */
	while(_right_fork->take(this));
	...
}
```
