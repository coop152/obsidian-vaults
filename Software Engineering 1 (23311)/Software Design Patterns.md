## Gang of Four
These patterns come from a book called "Design Patterns: Elements of Reusable Object-Oriented Software", most commonly known as the "Gang of Four" (GOF) Book. There are 23 in total, but only 6 are required for the exam.
Each pattern is described using this standard form:
- Name
- Intent
- Motivation
- Structure
- Implementation
- Applicability
## Types of Design Patterns
Each pattern falls into one of three categories: Creational, Structural, or Behavioural.
-   Behavioural Patterns are concerned with the way objects interact and the responsibilities they adopt, e.g. how they communicate, how behaviour is distributed, how tightly objects are coupled.
-   Structural Patterns are concerned with the composition of objects, e.g. how classes and objects can be composed to form larger structures.
-   Creational Patterns are concerned with the way objects are created, providing solutions to instantiate objects in the best possible way for a given situation.
## Strategy Pattern
### Intent
> “Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.”

Create a series of objects that hold many interchangeable algorithms.
### Refactoring
Often involves pulling the algorithm itself out of the client object and into a strategy object.
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
### Consequences
-   Can switch between different strategies at runtime
-   Can add a new strategy easily by adding a new concrete strategy class
## State Pattern
### Intent
> “Allow an object to alter its behaviour when its internal state changes. The object will appear to change its class.”

Create a series of objects that represent different states the client object may be in, each with a different functionality. Which one is used depends on the internal state of the client.
### Refactoring
Often involves removing conditional statements (e.g. if/else chains, case statements) and pulling out each branch into a state object.
### Example
![](Pasted%20image%2020230122143159.png)
![](Pasted%20image%2020230122143210.png)
Code looks somewhat like Strategy Pattern, but the "strategy" changes continually as the object's state changes and the State object is switched out.
### Consequences
cant be bothered i already know how it works
## Composite Pattern
### Intent
> “Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly.”

Implementing a Composite pattern means creating a common base class that represents both a part of an object, and the containing object as a whole.
### Refactoring
Consider refactoring to a composite pattern when data or code forms an implicit tree structure.
### Example
![](Pasted%20image%2020230122143809.png)
```java
interface FileSystemElement {
	public bool delete();
	public String getName();
	public void setName(String name);
	// etc..
}

class File implements FileSystemElement {
	public bool delete() { /* implementation goes here */ }
	public String getName() { /* implementation goes here */ }
	public void setName(String name) { /* implementation goes here */ }
}

class Folder implements FileSystemElement {
	private List<FileSystemElement> contents = new ArrayList<>();
	// shared methods
	public bool delete() { /* implementation goes here */ }
	public String getName() { /* implementation goes here */ }
	public void setName(String name) { /* implementation goes here */ }

	// composite only methods
	public void add(FileSystemElement child) {
		contents.add(child);
	}
	public void remove(FileSystemElement child) {
		contents.remove(child);
	}
	public FileSystemElement getChild(int idx) {
		return contents.get(idx);
	}
}
```
### Consequences
## Adapter Pattern
### Intent
### Refactoring
### Example
### Consequences
## Factory Method
### Intent
### Refactoring
### Example
### Consequences
## Singleton Pattern
### Intent
### Refactoring
### Example
### Consequences