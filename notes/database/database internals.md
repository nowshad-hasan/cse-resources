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
Now, interestingly we can solve Non-repeatable read by locking mechanism in that special row where we worked on a specific version. But how can we get rid of this Phantom read? Because this row not exists at the start of TX1, so we can not lock it and always get the update value for unbounded query. Phantom read is all about `having inserted a new row`.

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

Synchronous replication gives us strong consistency where asynchronous replication gives us weak consistency.

Note: We can apply `repeatable read` isolation level to apply repeatable read in our transaction. But what about Phantom Read? To get rid of that, we must apply `Serializable` in our transaction for MySQL, Oracle, SQL server etc except Postgres. In Postgres, in isolation level `Repeatable Read`, phantom read also does not exist.

Here is nice explanation about Serializable vs Repeatable Read. - https://www.udemy.com/course/database-engines-crash-course/learn/lecture/25758282#content

Consistency & Eventual Consistency:

- If a transaction committed a change will a new transaction immediately see the change?
- Both relational and NoSQL databases suffer from this when we want to scale horizontally or introduce caching.
- Eventual consistency

Consistency in two terms

- In reads
- In writes
Eventual consistency means we will get updated value very soon (as master-slave architecture), but that does not mean that our database is crashed, or no atomicity or durability - if it is like that then our db will not be eventually consistent.

Quiz 1: To ensure durability, database systems write to changes of the transaction to disk. However for those changes to persist on disk they should force the OS to flush to disk instead of OS cache, this is done through which OS operation?
Ans: fsync

**Note:** The transaction will always see the changes it makes regardless of the isolation level. Isolation level only applies to other concurrent transactions.

### Understanding Database Internals

Row_id

- Internal and system maintained. In some db, like - MySQL, InnoDB it is the same as table's primary key but other db like - Postgres has a system column row_id.

Page

- Depending on the storage model (row vs column store), the rows are stored and read in logical pages.
- A database does not read a single row, it reads a page or more in a single IO and we get a lot of rows in that IO.
- Each page has a size (8KB for postgres, 16 KB in MySQL)
- Each page holds 3 rows in this example, with 1001 rows we will get 1000/3 = 333 pages.

IO

- IO operation is a read request to the disk
- We try to minimise as much as possible.
- An IO can fetch 1 page or more depending on the disk partitions and other factors.
- IO cannot read a single row, its a page with many rows in them, you get them for free.

Heap

- Heap is a data structure where the table is stored with all its pages one after another.
- This is where the actual data is stored including everything.
- Traversing the heap is expensive as we need so many data to find what we want.
- That is why we need indexes that help us exactly what part of the heap we need to read. What pages of the heap we need to pull.

Index

- An index is another data structure separate from the heap that `pointers`.
- It has part of the data and used to quickly search for something.
- We can index on one column or more (but not all)
- Once you find a value of the index, you go the heap to fetch more information where everything is there. And it tells us exactly which page to fetch in the heap instead of taking the hit to scan of every page in the heap.
- Index is also stored as pages and cost IO to pull the entries of the index.
- The smaller the index, the more it can fit in memory the faster the search.
- Popular DS for index is B-Tree.

Notes:

- Sometimes the heap table can be organised around a single index. This is called a clustered index or an Index Organised Table
- Primary Key is usually a clustered index unless otherwise specified.
- MySQL InnoDB always have a primary key other indexes point to the primary key "value".
- Postgres only has secondary indexes and all indexes point directly to the row_id which lives in the heap.

Here is the video - https://www.udemy.com/course/database-engines-crash-course/learn/lecture/29082924#content

#### Row Oriented (Row Store) vs Column Oriented (Column store) Databases

Row Oriented Databases

- Tables are stored as rows in disk
- A single block IO read to the table fetches multiple rows with all their columns
- More IOs are required to find a particular row in a table scan but once you find the row you get all columns for that row.

Column Oriented Database

- Tables are stored as columns first in disk
- A single block IO read to the table fetches multiple columns with all matching rows
- Less IOs are required to get more values of a given column. But working with multiple columns require more IOs.

**Note:** Don't `select *` whether it is row store or column store.

Pros & Cons

Row store:

- Optimal for read/ writes
- powerful for online transaction processing
- Compression not efficient
- Aggregation not efficient
- Efficient queries with multi-columns
- Very very simple which is main power

Most BD like Postgres, Oracle, MySQL are row based.

Column store (used in lots of warehouse data analytics)

- Writes are slower
- Online analytic processing
- Compress greatly
- Amazing for aggregation
- Inefficient queries with multi columns

Note: In some DB, tables can be stored in both ways in a database, row based and column based. But we can not join row based table with column based table, which is bad.

The video - https://www.udemy.com/course/database-engines-crash-course/learn/lecture/23084542#content

#### Primary Key vs Secondary Key

Here is the video - https://www.udemy.com/course/database-engines-crash-course/learn/lecture/26775206#content

Primary key is clustered index - it is ordered physically. In postgres, secondary key are un-clustered and all indexes are by default secondary - that means a separate structure to hold the index - when we search for some value we need to go that structure at first, then we go to actual heap (disk) where our data is stored.

Database Pages - https://www.udemy.com/course/database-engines-crash-course/learn/lecture/37150148#content

### Database Indexing

Let's create a table with million rows - 

```sql
create table temp(t int);
insert into temp(t) select random()*100 from generate_series(0,1000000);
```

Indexing is basically two types - 
1. B Tree
2. Elasant tree

If we now do the command `\d employees`, we will see that our primary key has an index which is a `B tree`.

Now let's run a simple select query with our index id.

`explain analyze select id from employees where id = 2000;`

After running this command, we will see something like that `Heap Fetches: 0`, it means that we did not need to go to our heap for further data, we got from our index what we need.

`Planning Time` is like should our query go to index and search for data or should it go to heap - the decision making planning.

`Execution time` is the actual execution time.

Note: Table is another data structure and index is another, table is heavier than index.

Let's run another query - `explain analyze select name from employees where id = 5000;` - it will take more time compared to before because now we are fetching name which has no index and will see NO `Heap Fetches: 0` for this.

Interestingly if we run this query again, we will see faster timing, as the result is now cached. Now, if we change the id like - 5001, we will see same faster result as it's not exact id which is cached but the `page` is cached. We can see the proof if we change id = 7834.

Let's change the query like that 
`explain analyze select id from employees where name = 'Zs';`
. Before this query we were seeing something like that - `Index Scan using employees_pkey on employees` but now we will see that `Parallel Seq Scan on employees` - a full table scan and a huge time. But postgres is trying to little bit smarter by launching - `Workers Planned: 2` - worker threads. `Planning time` is faster as it knows what to do very early.
Now let's change this again - 
`explain analyze select id from employees where name ilike '%Zs%';` - it is the worst query.

Let's create an index for our name column - 
`create index employees_name on employees(name);` - it will take a little bit of time as it is creating another data structure - B Tree to organise this. Let's see table's structure again with `\d employees`. We will see two indexes now.
`explain analyze select id from employees where name = 'ko';` - it will be faster than without index. 

**Note:** In MySQL, every index has the primary index associated with. So, pulling `id, name` is not costly now as name is indexed and id is connected with it and query does not need to hit on `table heap`.

Let's run `explain analyze select id from employees where name ilike '%xs%';` - still slower query, index not working as ilike is an expression so index is with no use at all. 
Another worst query is `select *` - as it fetches data from heap and may be the table uses blob or many columns which are not indexed. So it is advisable that we should fecth columns which are indexed.

#### Understanding SQL Query Planner and Optimizer with Explain

Let's create another table 
```sql
create table grades (
id serial primary key, 
 g int,
 name text 
); 


insert into grades (g,
name  ) 
select 
random()*100,
substring(md5(random()::text ),0,floor(random()*31)::int)
 from generate_series(0, 500);

vacuum (analyze, verbose, full);
create index g_idx on grades(g);
```

Now a select query - `explain select * from grades;`
We will see something like that -  `Seq Scan on grades` - again a frl