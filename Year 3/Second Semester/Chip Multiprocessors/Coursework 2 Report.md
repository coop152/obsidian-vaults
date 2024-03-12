# Question 1
There is a race condition in `Philosopher::eat()`, where the Philosopher tries to get the locks on the two forks. 
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

# Question 2
This code is likely to cause a deadlock, as there is a period of time in-between 