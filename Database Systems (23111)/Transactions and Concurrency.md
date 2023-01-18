A transaction in a DBMS is a collection of operations that are connected such that they represent one single "action", or one unit of "work" as a whole. For example, a transaction may involve a single operation of simply adding a new row to a table, or it may involve multiple operations of retrieving some data, adding a new row and then updating an existing row. Transactions are **Atomic**, meaning that they may only be completed in their entirety or not at all; this is important for maintaining data integrity on a database, as if an action is not completed entirely (for example, one row is added but then another related row cannot be updated) then the database may be left in an invalid state. It is also important for concurrency, as if another user were to interact with data that is in the process of being changed, it could give incorrect or even invalid results.
## Concurrency Risks
#### The Lost Update
![[Pasted image 20230118150826.png]]
When one user is allowed to read a value while another user is in the process of changing it, there is a possibility that the interrupting user may miss an update that the other user made, overwriting it when they make their own changes.
#### The Temporary Update (Dirty Read)
![[Pasted image 20230118151324.png]]
If transaction 1 makes an update that must later be rolled back due to a failure of some sort, transaction 2 may have read the updated value before it was rolled back, meaning that transaction 2 has a false value. 
#### The Incorrect Summary
#### The Unrepeatable Read
