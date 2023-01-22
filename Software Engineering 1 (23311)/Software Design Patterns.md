## Gang of Four
These patterns come from a book called "Design Patterns: Elements of Reusable Object-Oriented Software", most commonly known as the "Gang of Four" (GOF) Book. There are 23 in total, but only 6 are required for the exam.
Each pattern is described using this standard form:
- Name
- Intent
- Motivation
- Structure
- Implementation
- Applicability
## Types of Design Patterns#
Each pattern falls into one of three categories: Creational, Structural, or Behavioural.
-   Behavioural Patterns are concerned with the way objects interact and the responsibilities they adopt, e.g. how they communicate, how behaviour is distributed, how tightly objects are coupled.
-   Structural Patterns are concerned with the composition of objects, e.g. how classes and objects can be composed to form larger structures.
-   Creational Patterns are concerned with the way objects are created, providing solutions to instantiate objects in the best possible way for a given situation.
## Strategy Pattern
### Intent:
> “Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.”

Create a series of objects that hold many interchangeable algorithms.
### Example
![](Pasted%20image%2020230122135430.png)
```java
// Client class
class User {
	private SortingStrategy sortingStrategy;

	public User(SortingStrategy strategy) {
		this.sortingStrategy = strategy;
	}

	public doSomething() {
		// .. do stuff
		// need to sort a list!
		sortingStrategy.sort(stuff);
		// .. do more stuff
	}
}
interface SortingStrategy {
	public void sort(int[] arr);
}

class MergeSortStrategy implements SortingStrategy {
	public void sort(int[] arr) {
		// implementation
	}
}

class InsertionSortStrategy implements SortingStrategy {
	public void sort(int[] arr) {
		// different implementation
	}
}
```
## State Pattern
## Composite Pattern
## Adapter Pattern
## Factory Method
## Singleton Pattern