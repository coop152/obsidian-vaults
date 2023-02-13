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

## Data Access Architecture
- Model has the Database, along with a Repository that abstracts away the database interaction.
- Controller accepts HTTP requests from the View, and submits requests to the Model