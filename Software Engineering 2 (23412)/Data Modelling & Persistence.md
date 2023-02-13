Start with:
## POJOs (Plain Old Java Objects)
```java
public class Venue {
	private long id;
	private String name;
	private int capacity;

	public Venue() {
	
	}

	// getters and setters go here
}
```

## Annotate for persistence
To tell spring to persist this data in the database
```java
@Entity
@Table(name = "venues")
public class Venue {
	@Id
	private long id;
	private String name;
	private int capacity;

	public Venue() {
	
	}

	// getters and setters go here
}
```

## Relationships
- `@OneToMany`, `@ManyToOne`, `@OneToOne` Annotations

## Spring's Data Access Architecture
![](Pasted%20image%2020230213142503.png)

## Full Example
Repository:
```java
public interface VenueRepository extends CrudRepository<Venue, Long> {
	// marker type; just tells spring to make the type for you
}
```
Default implementation provides:
- `count()`
- `findOne(long id)`, `findAll()`
- `save(Venue venue)`, `delete(long id)`
- and more
Define queries you want as methods and spring will automagically implement them for you:
```java
public interface VenueRepository
```