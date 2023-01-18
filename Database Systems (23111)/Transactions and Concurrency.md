A transaction in a DBMS is a collection of operations that are connected such that they represent one single "action", or one unit of "work" as a whole. For example, a transaction may involve a single operation of simply adding a new row to a table, or it may involve multiple operations of retrieving some data, adding a new row and then updating an existing row. Transactions are **Atomic**, meaning that they may only be completed in their entirety or not at all; this is important for maintaining data integrity on a database, as if an action is not completed entirely (for example, one row is added but then another related row cannot be updated) then the database may be left in an invalid state. It is also important for concurrency, as if another user were to interact with data that is in the process of being changed, it could give incorrect or even invalid results.
## Concurrency Risks
#### The Lost Update
![[Pasted image 20230118150826.png]]
When one user is allowed to read a value while another user is in the process of changing it, there is a possibility that the interrupting user may miss an update that the other user made, overwriting it when they make their own changes.
#### The Temporary Update (Dirty Read)
![[Pasted image 20230118151324.png]]
If transaction 1 makes an update that must later be rolled back due to a failure of some sort, transaction 2 may have read the updated value before it was rolled back, meaning that transaction 2 has a false value. 
#### The Incorrect Summary
Dont care
#### The Unrepeatable Read
Dont care

## ACID Transactions
- Atomicity: Either *all* operations in the transaction are completed, or *none* of them are
- Consistency Preservation: The database should never be left in an inconsistent state
- Isolation: The transactions appear to be working completely in isolation, even though they are working concurrently
- Durability (Permanency): In the event of a failure, the effects of a completed transaction must never be lost.

## Schedules
![[Pasted image 20230118153133.png]]
- When executing transactions concurrently, we are actually interleaving operations in a serial fashion; we need a schedule
- A schedule is defined as $S$ of $n$ transactions $T_1, T_2, \dots, T_n$ 
- There is a shorthand notation for describing a schedule, using the symbols $b$, $r$, $w$, $e$, $c$ and $a$. That is: Begin, Read, Write, End, Commit and Abort
- For example: $S_a:r_1(X);r_2(X);w_1(X);r_1(Y);w_2(X);w_1(Y);$ 
	- Transaction 1 reads X
	- Transaction 2 reads X
	- Transaction 1 writes X
	- Transaction 1 reads Y
	- Transaction 2 writes X
	- Transaction 1 writes Y
- This particular schedule is invalid; transaction 1's update of X is lost, as it is written after transaction 2 reads X.

## Conflicting Operations in a Schedule
- Two operations in a schedule are said to conflict if all three of these things are true:
	- They belong to two different transactions
	- They access the same item
	- At least one of the operations is a write
- Conflicting operations may result in inconsistent data!
![[Pasted image 20230118154334.png]]
## Complete Schedules
- The goal is to establish a **complete** schedule. A complete schedule satisfies these requirements:
	- Contains all of the operations from all transactions
	- Maintains the relative order of the operations within each transaction
	- For any two conflicting operations, one of the two must occur before the other.
#### The easy way - Serial schedules
![[Pasted image 20230118155223.png]]
- If each transaction is allowed to fully complete before another starts, there won't be any issues.
- This is unacceptable in practice: A large transaction can completely block all other transactions, and any I/O waits will leave the database idle.
#### The hard way - Interleaving
![[Pasted image 20230118155527.png]]
- The transactions are allowed to take turns running. This stops large transactions from hogging the database, and allows the database to continue working while waiting on I/O.
- However, it isn't trivial to make a correct interleaved schedule. Schedule C is not valid! 
- 