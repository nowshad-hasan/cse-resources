This file is output of https://www.udemy.com/course/database-engines-crash-course/

### ACID 

- Atomicity - single unit of work
- Isolation - whether the transaction is isolated from each other. What another transaction can see.
- Consistency - If the transaction is consistent that means our data is okay.
- Durability - our transaction data is written to the disk successfully. If now, database is crashed or power down, no worries, our data is safe.

What is a transaction?

- A collection of queries
- One unit of work

Interestingly DB automatically wrapped insert, update and other operations in a transaction and commits it. It is done under the hood.

Lifespan of Transaction

- Transaction BEGIN
- Transaction COMMIT
- Transaction ROLLBACK (To a SAVEPOINT)
- Transaction unexpected ending, like - = ROLLBACK

Let's talk about Rollback for a time being. If we implement a DB, how we gonna implement ROLLBACK? Maybe we will write to disk at first (that means commit is faster as Postgres does this), then after COMMIT we don't have to work very much, and for this ROLLBACK is expensive, as data is already written on disk.  

Another way could be - save all the data on memory, then COMMIT will be expensive, as these need to be written on disk but ROLLBACK will be faster as just we need to clear the memory.

Nature of Transactions

- Usually Transactions are used to change and modify data
- But Read-only transactions are also possible where db kind od add some tweaks for data-read. Like - generate a report for a snapshot of time, there we need Consistent data.
#### Atomicity

This name came from the 'Atom'. That means the minimum single unbreakable unit. In short, the transaction will be committed or rolled back. Here some specifications - 

- All queries in a transaction must succeed.
- If one query fails, all prior successful queries should rollback.
- If database went down (like - crash) prior to a commit of a transaction, all the successful queries in the transactions should rollback. The database should clean this up after restart.
- An atomic transaction is a transaction that will rollback all queries if one or more queries failed.

For DB crash, when db is restarted, it may take hours to remove all the in-middle uncommitted transaction data. It needs to clear all the garbage values otherwise our db will have inconsistent data. Some databases simply can not be started unless it cleans up the data.

#### Isolation

The whole point of Isolation is - whether a transaction is separated from other, e.g. - Covid Isolation Center. In short - 
**Can my inflight transaction see changes made by other transactions?**

We have Side-Effects of transaction isolation, that we called Read phenomena. We will explain this side-effects with an example below :-

Let's say we 

- Dirty Reads - getting uncommitted data from other transaction
- Non-repeatable reads - the reads which are non-repeatable, that means if we repeat data read, we will see different results for same row value as other transaction made the changes. (Remember this in a way - we should not repeat the data-read, if we do we will get different values, that is why it is called non-repeatable read)
- Phantom read - Kind of non-repeatable read, but here difference is - other transaction insert and committed a new row, then our own transaction fetch data in a range query and get that new inserted row value.
- Lost updates - two concurrent transactions read a same row value and gonna write that row value. We made a change and commit. And then other transaction made the changes on the old value without reading my new changes and commits that. And my change got lost.
  
Let's see some example.
We have a Sales table. And two rows -

PID         QNT     Price
Product 1 -> 10 ->  $5
Product 2 -> 20 ->  $4
#### Dirty Reads

TX1

select pid, qnt* price from sales
Product 1, 50
Product 2, 80
(not committed)

TX2
update sales set qnt = qnt + 5 where pid = 1;
(not committed)

TX1
(select again)
select sum(qnt*price) from sales; 
[should be = 130, will be = 155 (15*5) for dirty read configuration]

TX2
(something bad happened)
ROLLBACK (setting PID.QNT=10)
(complete)

TX1 - also complete and committed.

Now, we got inconsistent data from TX1.
This is the worst phenomena of Isolation. And most of databases don't support this isolation now-a-days.

#### Non-repeatable read

TX1
select PID, QNT*price from sales

TX2
update sales set qnt = qnt + 5 where pid = 1;
Committed

TX1
select sum(qnt*price) from sales.
complete and committed.

We will again get 155 from TX1 but should be 130. It was not dirty value as the value was committed. 
Interestingly fixing the non-repeatable read is not easy that means getting the same data as from the transaction start and get that same value until committed. Then the DB designer must maintain a version or other mechanism for this types of things. Postgres always creates a version for same value. But MySQL, Oracle keeps another table like `Undo` for versioning which is kind of expensive.

#### Phantom read

TX1
select PID, QNT*price from sales;

TX2
insert into sales values ('Product 3', 10, 1);
Commit TX2

TX1
select SUM(QNT*PRICE) from sales;
complete and commit

It will see 140$. 
Now, interestingly we can solve Non-repeatable read by locking mechanism in that special row where we worked on a specific version. But how can we get rid of this Phantom read? Because this row not exists at the start of TX1, so we can not lock it and always get the update value for unbounded query.

#### Lost Updates

TX1
(read QNT=10 for PID=1)
update sales set QNT = QNT + 10 where pid = 1

TX2
(read QNT=10 for PID=1)
update sales set QNT = QNT+5 where pid = 1;
commit TX2

TX1
select SUM(QNT*PRICE) from sales;
Complete and commit TX1;

Should be 180, but TX1's update gets lost by TX2. So, value will be 155.

To solve all these side-effects there are some ISOLATION levels in database design.

- Read uncommitted - No isolation at all, anything can be happened. But the fastest one.
- Read committed - can see transaction committed changes by other transactions
- Repeatable read - transaction will make sure get a row value will remain un-changed till the transaction ending.
- Snapshot - each query in a transaction only sees value at the start of the transaction. It's like a snapshot version of the database on the moment.
- Serializable - slowest one. Transactions run one by after. So no side effects at all.

Each DBMS implements this isolation levels differently. Here is [link](https://en.wikipedia.org/wiki/Isolation_(database_systems)#:~:text=Isolation%20is%20typically%20defined%20at,the%20use%20of%20temporary%20tables.) from wikipedia.

Database implementation of Isolation

- Pessimistic - row level locks, table locks, page locks to avoid lost updates. It's very expensive.
- Optimistic - No locks, just track if things changed and fail the transaction if so.
- Repeatable read 'locks' the rows it reads and it could be expensive if we read a lots of rows. Postgres implements this Repeatable read as snapshot and that's why Phantom read does not occur in Repeatable Read Isolation for Postgres.
- Serializable are usually implemented with optimistic concurrency control otherwise serialisable would be much more slow.


### Consistency
How could we maintain consistency?

- Atomicity
- Referrel integrity (foreign keys)
- Isolation

Interestingly when we update a row and getting the latest data is the most challenged thing in DB. Because there are master-slave replica thing. So, that is a hard part and NoSQL like MongoDB suffer from this situation. To solve this problem, another term `eventual consistency` came, which is almost consistent. It means that maybe right now we are not consistent, but will be eventual consistent somehow.

Synchronus replication gives us strong consistency where asynchronus replication gives us weak consisetncy.