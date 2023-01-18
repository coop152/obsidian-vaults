- Can store vast amounts of data more effectively than a traditional SQL database (good for companies like Twitter or Google)
- Highly scalable
## MongoDB
| SQL Term        | MongoDB Equivalent |
| --------------- | ------------------ |
| Database        | Database           |
| Table           | Collection         |
| Record/Row      | Document           |
| Normalisation   | Referencing        |
| Denormalisation | Embedding          |
## CRUD Operations (MongoDB)
### Create
**SQL**:
```sql
INSERT INTO Table(aNumber, aString, aFloat) VALUES (10, "abc", 5.9);
```
**MongoDB**:
```js
db.Table.insert({
	aNumber: 10,
	aString: "abc",
	aFloat: 5.9
})
```
### Read
**SQL**:
```sql
SELECT x FROM Table WHERE y > 5 AND z = 2;
```
**MongoDB**:
```js
db.Table.find(
	{y: {$gt: 5}, z: 2}, 
	{x: 1}
)
```

### Update
**SQL**:
```sql
UPDATE Table SET x = 2, y = 3 WHERE z > 5;
```
**MongoDB**:
```js
db.Table.updateMany(
	{z: {$gt: 5}},
	{x: 2, y: 3}
)
```

### Delete
