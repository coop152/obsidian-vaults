# Question 1
The program can enter an invalid state in `Fork::take()` if two Philosophers try to take a fork at the same time. The intended behaviour is that whichever philosopher is first sees that `fork._free` is true, then immediately sets `fork._free` to false so no others can take it. However, It is possible for another philosopher to check `_free` and get past the check before the first philosopher has changed it to false. In this case they both believe they have taken the fork, but only one will be assigned as the actual holder according to `fork._holder`. Here is an example run that illustrates this race condition:

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
In all cases this will lead to invalid behaviour in `Fork::release()`, when the philosophers are done eating. If the philosopher that was stored as the holder releases the fork first, then 

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
