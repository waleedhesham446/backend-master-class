# ACID

* Atomicity:
- Either all operations complete successfully or the transaction fails and the db is unchanged

* Consistency:
- The db state must be valid after the transaction. All constraints must be satisfied

* Isolation:
- Concurrent transactions must not affect each other

* Durability:
- Data written by a successful transaction must be recorded in persistent storage

## Read Phenomena (happen if the database is running at a low level of transaction isolation)

1- Dirty Read:
  - A transaction reads data written by other concurrent uncommitted transaction

2- Non-Repeatable Read:
  - A transaction reads the same row twice and sees different value because it has been modified by other committed transaction after the first read

3- Phantom Read:
  - A transaction re-executes a query to find rows that satisfy a condition and sees a different set of rows, due to changes by other committed transaction

4- Serialization Anomaly:
  - The result of a group of conncurrent committed transactions is impossible to achieve if we try to run them sequentially in any order without overlapping
  - Example, 2 conncurent transactions are doing the same aggregation, then insering a new record that affects the aggregation value. Conncurrently, they both will read the same value. But if they were executed serially, there is no way that both will read the same aggregation value as the second one will read after first one updates

## 4 Standard Isolation Levels

1- Read Uncommitted:
  - Can see data written by uncommitted transaction
  - PostgreSQL doesn't have this level, it's equivalent to Read Committed

2- Read Committed:
  - Only see data written by committed transaction

3- Repeatable Read:
  - Same read query always returns same result

4- Serializable:
  - Can achieve same result id execute transactions serially in some order instead of conncurrently
  - MySQL implicitly converts all plain `SELECT` query to `SELECT FOR SHARE`
  - A transaction that holds `SELECT FOR SHARE` only allows other transactions to READ the rows, but not UPDATE or DELETE them
  - With this locking mechanism, the inconsistent data scinario in previous levels is no longer possible
  - The lock has a timeout duration, so if the second transtion doesn't commit or rollback to release the lock within that duration, a `lock with exceeded timeout` error is thrown 
  - Need transaction retry strategy
  - In PostgreSQL, the second transaction is refused and you can execute it again
  - In MySQL, the second transaction is locked until first transaction is committed
  - In MySQL, if both transactions run the `SELECT` before the first one runs the `INSERT`, a deadlock will happen then the deadlocked transaction need to be run again