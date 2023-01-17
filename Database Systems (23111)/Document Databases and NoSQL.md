- Can store vast amounts of data more effectively than a traditional SQL database (good for companies like Twitter or Google)
- Highly scalable
## MongoDB
| SQL Term        | MongoDB Equivalent |
| --------------- | ------------------ |
| Database        | Database           |
| Table           | Collection         |
| Record/Row      | Document           |
| Normalisation   | Embedding          |
| Normalised      | Embedded           |
| Denormalisation | Referencing        |
| Denormalised    |                    |


## CRUD Operations (MongoDB)
### Create
**SQL**:
```sql
INSERT INTO Table(aNumber, aString, aFloat) VALUES (10, "abc", 5.9);
```
**MongoDB**:
```python
db.Table.insert({
				 aNumber: 10,
				 aString: "abc",
				 aFloat: 5.9
				 })
```
### Read
**SQL**:
```sql
SELECT * FROM Table;
```
**MongoDB**:
```python
db.Table.find()
```

### Update

### Delete
