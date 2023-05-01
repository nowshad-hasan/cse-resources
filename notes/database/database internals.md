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

** explain**: What query plan postgres will use for a given query. It's not exact query execution but an estimation checking.

Now a select query - `explain select * from grades;`
We will see something like that -  `Seq Scan on grades` - again a full table scan - it's a query plan. Sometimes postgres uses parallel scan with multiple threads.
Result of the query is - ` Seq Scan on grades  (cost=0.00..9.01 rows=501 width=22`. Explanation is given below - 

- cost - 0.00 -> milliseconds needed to fetch the first page. 9.01 -> time needed to finish query (nothing but an estimation)
- rows - It's an interesting value. It shows us an estimation row count of the table. It's not exact count. But it can be helpful when we count likes of instagram, we don't need exact count but an estimation. And select count(id) or something like that will kill us. So, we can execute `explain` and get the estimation very fast.
- width - sum of column data types.

`explain select * from grades order by g;` gives us 
`Index Scan using g_idx on grades  (cost=0.15..31.65 rows=501 width=22) (1 row)` - here we are seeing that cost is little bit high as now postgres needs to do some work as we `order by` data but still fast as it is using `index scan using g_idx`. Rest of the data is same.

`explain select * from grades order by name;` gives us 

```sql
Sort  (cost=31.48..32.73 rows=501 width=22)
   Sort Key: name
   ->  Seq Scan on grades  (cost=0.00..9.01 rows=501 width=22)
(3 rows)
```

Cost is very high now as we are sorting by name where it has no index. So postgres needs to go to heap to fetch data and sort.

**Note:** When we explain something, we need to start from the bottom. In above query, planning for `Seq Scan on grades  (cost=0.00..9.01 rows=501 width=22) (3 rows)` - means very fast, but when we are doing the sort, it takes the time.

`explain select id from grades;` gives us `Seq Scan on grades  (cost=0.00..9.01 rows=501 width=4) (1 row)` - here interesting part is width - 4, as id is integer and 4 byte length.

`explain select name from grades;` gives us `Seq Scan on grades  (cost=0.00..9.01 rows=501 width=14)` - width is now 14, as the name is text which is a variable length. So, we must be careful about blob or large text value - because large data means large data communication in network, tcp connection will take time.

`explain select * from grades where id = 10;` gives `Index Scan using grades_pkey on grades  (cost=0.27..8.29 rows=1 width=22)  Index Cond: (id = 10)` - it takes little time as it is now index scanned.

#### Seq table scan Vs index scan Vs Bitmap index scan

query - `explain select name from grades where id = 1000;` 
output 
```sql
 Index Scan using grades_pkey on grades  (cost=0.27..8.29 rows=1 width=1
4)
   Index Cond: (id = 1000)
```

As we already know, index and table are totally different data structure. So, for the above query, postgres finds the row at first using index, then goes to heap to fetch the name field.

Note that, we have 501003 rows on the table.
query - `explain select name from grades where id < 1000;`
If we run above query postgres is smart enough to know that id below than 1000 is not much and id is on btree. So, it will use the same technique like before to finds the ids and fetch the value from the heap. output is - 

```sql
 Index Scan using grades_pkey on grades  (cost=0.42..23.88 rows=483 widt
h=15)
   Index Cond: (id < 1000)
```

query - `explain select name from grades where id > 1000;`

Now, the postgres is smart enough to `understand` that this query is expensive and it gonna fetch almost all the values from heap - so if it runs the `index scan`, it will first go to index, then heap, then again index and then heap - so it is doing more and more. So, it will decide to do a full table scan - which is `seq scan` - not back-forth from index to heap anymore. And it is really cheaper now. For index scan it will do two things - 'go to index + go to heap', for `seq scan`, it will do 'go to heap'. For large query resultset, it is cheaper.

Output - 

```sql
Seq Scan on grades  (cost=0.00..9628.28 rows=500018 width=15)
   Filter: (id > 1000)
```

**Note:** Initially my table rows was only 500. For above two queries, it shows the opposite. Because in that case `>1000` has now row, as it finds it from btree. But for `<1000` it gives all the rows - so it tries to find all the rows with `seq scan` which is cheaper.

But sometimes, the query result is not 90% as of our second case above. The result might be huge enough (but not almost all the rows) to use an `existing index`. Then postgres will use `bitmap scan`. Because using 'index scan' for every value we find, is expensive as every-time it finds a row with index and needs to go to heap.

query - `explain select name from grades where g > 95;`
output - 

```sql
 Bitmap Heap Scan on grades  (cost=229.27..3854.26 rows=20239 width=15)
   Recheck Cond: (g > 95)
   ->  Bitmap Index Scan on g_idx  (cost=0.00..224.22 rows=20239 width=0
)
         Index Cond: (g > 95)
```

Postgres is gonna build a bitmap - which is basically an array of bits (0 and 1) - bit is just a page number - bit number 0 is page number 0, bit number 1 is page number 1. What is does it finds a row that satisfies the where condition, then it finds the page for that row (let's say 9), now it goes to the bitmap and set the index of that bitmap array (9) to 1. No, postgres is not going to heap yet. It will build a beautiful bitmap array with all the pages found for that query. After the bitmap creation, now it will go to the heap and fetch all the pages (we found from bitmap array) at once. Now, we found the pages which are collection of rows. Now postgres will fetch the rows from the pages with a `recheck condition` to filter the rows in which conditions are not met.
So, it is better than `index scan` and `seq scan`. Is not this fascinating?
But we have still more to say.
query - `explain select name from grades where g > 95 and id < 100000;` 

The above query is fascinating. Because now postgres will build a bitmap from two indexes (`BitmapAnd`) - first from index scan then from bitmap scan - it actually merges the results together in the bitmap - then goes to the heap, fetch pages - recheck condition - fetch all the rows at last.

In my pc, it is doing `index scan on grades_pkey`. But it works on that way.


#### Key vs non-key column indexes

We need to start our docker postgres with 1GB shared memory like below - 

`docker run --name pg —shm-size=1g -e POSTGRES_PASSWORD=postgres —name pg postgres`


Query - `explain analyze select id, g from students where g > 80 and g < 95 order by g desc`

It is a painful query as we are giving condition on `g` but there is not index on g. So, it fetches pages at first, then filters out all the non-matching rows.

Let's create an index over `g` with below query - 

`create index g_idx on students(g);`

Let's run the previous query again. It will use index but the problem is about fetching id, g. g is ok as it is on the btree. id is also on btree but not from g, so postgres needs to go to heap to fetch id for all the candidate rows selected by where condition,  means g indexed btree.

So, query is still slow but better than previous situation where we have no index over `g`.

But let's try the above query with a limit - 

`explain analyze select id, g from students where g > 80 and g < 95 order by g desc limit 1000;`

Now it will be faster a little. But sometimes our query got cached. Let's change the query a little.
`explain (analyze, buffers) select id, g from students where g > 80 and g < 95 order by g desc limit 1000;`

We will see `shared hit: {some value}`.

So to get rid of cache, we must change the where condition with - `g > 10 and g< 10` or something as our need. If we again analyse the query we will see that our query is slow again for previous reason - needs to go to table for fetching id.

So the question is how to get data from only index without going to heap that means table (index only scan)?

Let's drop the previous index of `g` and create again like below - 
`create index g_idx on students(g) include (id)`

Creating index now will take a little bit of less time as now it includes `id` with it.
Here g is the key column, id is the non-key column of index `g_idx`.

Now, let's run that query again. 

And now it is faster. If we see the values of the result, we will see that it is 
`Index only scan`
Heap fetches: 0

If heap fetches is > 0, that means we had to go to heap for any reason, maybe we did not run `vacuum` or something else.

As expected, if we run the same query again, we will see it faster than before because it is cached.

**Note:** If we see any problem regarding running this query, we can run `vacuum(verbose) students;` or `vacuum (verbose, full, analyze)` to update all the pages and other things.

So, if we run the previous query with a limit, that will be blazing fast.

**Note:** If we put more things into index, then index will not fit into memory, it will go to disk, and for this case if we query from index, we need to go to disk for index scan and then it will take time again. So, we have to careful about that.

#### Index Scan Vs Index Only Scan

Let's create a grades table without primary key.

```sql
create table grades(id integer, g integer, name varchar(200));

create sequence grades_seq start 1;

insert into grades (id, g,name  ) 
select 
nextval('grades_seq'), random()*100,
substring(md5(random()::text ),0,floor(random()*31)::int)
 from generate_series(0, 500);

vacuum (analyze, verbose, full);
```

Query - `explain analyze select name from grades where id = 7;`

Result - 
```sql
Seq Scan on grades  (cost=0.00..12.12 rows=1 width=418
) (actual time=0.012..0.013 rows=1 loops=1)
   Filter: (id = 7)
   Rows Removed by Filter: 6
 Planning Time: 0.072 ms
 Execution Time: 0.026 ms
```
So, it is sequential scan aka full table scan as no index present. `Rows Removed by Filter: 6` also means that manually rows are filtered.

Now, let's create an index on `id`.
`create index id_idx on grades(id);`

**Note**: Make sure you use a medium level large table here - at least 500 rows.

Now, let's run the same query and see the result.

result - 
```sql
Index Scan using id_idx on grades  (cost=0.27..8.29 ro
ws=1 width=14) (actual time=0.024..0.025 rows=1 loops=1
)
   Index Cond: (id = 7)
 Planning Time: 0.292 ms
 Execution Time: 0.042 ms
```

Here our index is used. But here to fetch the name - which is not indexed - we need to go to heap aka our table which is slower.

query - `explain analyse select id from grades where id = 7;`

result - 

```sql
Index Only Scan using id_idx on grades  (cost=0.27..8.
29 rows=1 width=4) (actual time=0.041..0.043 rows=1 loo
ps=1)
   Index Cond: (id = 7)
   Heap Fetches: 1
 Planning Time: 0.092 ms
 Execution Time: 0.059 ms
```

`index only scan` - Now we fetch data which is already present on the index (that means our btree). Yes, it is the fastest and very beautiful.

So, let's change our index a little bit (We discussed it in previous section).

```sql
drop index id_idx;
create index id_idx on grades(id) include (name);
 explain analyse select name from grades where id = 7;
```

result 

```sql
 Index Only Scan using id_idx on grades  (cost=0.27..8.
29 rows=1 width=14) (actual time=0.025..0.027 rows=1 lo
ops=1)
   Index Cond: (id = 7)
   Heap Fetches: 1
 Planning Time: 0.230 ms
 Execution Time: 0.043 ms
```

Now fetching name will use `index only scan` unlike before.

query - ` explain analyse select g from grades where id = 7;` is gonna use `Index scan`.

**Note:** We have to be careful about index getting larger and larger. Then postgres will need to fetch value from index at first and go to heap - which is costly.

#### Combining database indexes

Let's create a table called test.

```sql
create table test (a integer, b integer, c integer)

insert into test (a, b, c) 
select 
random()*100, random()*1000, random()*1000
from generate_series(0, 12000000);

vacuum (analyze, verbose, full);
```

Let's create index on a;

```sql
create index on test(a);
create index on test(b);
```

query - `explain analyze select c from test where a = 75;`

result - 

```sql
 Bitmap Heap Scan on test  (cost=1295.43..69192.49 rows
=116000 width=4) (actual time=25.325..1696.993 rows=119
772 loops=1)
   Recheck Cond: (a = 75)
   Heap Blocks: exact=54817
   ->  Bitmap Index Scan on test_a_idx  (cost=0.00..126
6.43 rows=116000 width=0) (actual time=14.855..14.863 r
ows=119772 loops=1)
         Index Cond: (a = 75)
 Planning Time: 0.093 ms
 Execution Time: 2660.858 ms
```

Yeah, postgres needs to go to heap to fetch `c` using index `test_a_idx`, that's why it is using `bitmap index scan`.

But let's say we run the same query with a limit 2; We will see now it will use `index scan` (for me it used seq scan). Because to build two rows, it does not need to build a bitmap which is costly.

Now, if we change the query as like - `explain analyze select c from test where b= 100;`, then as usual it will build a bitmap.

But,

query - `explain analyze select c from test where b= 100 and a = 200;`

result - 
```
Bitmap Heap Scan on test  (cost=772.37..1000.04 rows=5
8 width=4) (actual time=2.210..2.272 rows=0 loops=1)
   Recheck Cond: ((b = 100) AND (a = 200))
   ->  BitmapAnd  (cost=772.37..772.37 rows=58 width=0)
 (actual time=2.155..2.192 rows=0 loops=1)
         ->  Bitmap Index Scan on test_b_idx  (cost=0.0
0..133.66 rows=11896 width=0) (actual time=1.158..1.165
 rows=12122 loops=1)
               Index Cond: (b = 100)
         ->  Bitmap Index Scan on test_a_idx  (cost=0.0
0..638.43 rows=58400 width=0) (actual time=0.667..0.674
 rows=0 loops=1)
               Index Cond: (a = 200)
 Planning Time: 0.101 ms
 Execution Time: 2.341 ms
```

Here we see that postgres uses bitmap scan separately for both index and then merges together.
Let's change the query to or.
query - `explain analyze select c from test where b= 100 or a = 200;`

result - 

```sql
Bitmap Heap Scan on test  (cost=807.21..69559.86 rows=
70238 width=4) (actual time=3.694..127.476 rows=12122 l
oops=1)
   Recheck Cond: ((b = 100) OR (a = 200))
   Heap Blocks: exact=11065
   ->  BitmapOr  (cost=807.21..807.21 rows=70296 width=
0) (actual time=1.688..1.725 rows=0 loops=1)
         ->  Bitmap Index Scan on test_b_idx  (cost=0.0
0..133.66 rows=11896 width=0) (actual time=1.632..1.639
 rows=12122 loops=1)
               Index Cond: (b = 100)
         ->  Bitmap Index Scan on test_a_idx  (cost=0.0
0..638.43 rows=58400 width=0) (actual time=0.020..0.027
 rows=0 loops=1)
               Index Cond: (a = 200)
 Planning Time: 0.141 ms
 Execution Time: 223.437 ms
```

It is exactly same as before - it is using bitmap scan, then uses BitmapOr.

Let's drop the indexes. And create a composite index.

```sql
drop index test_a_idx, test_b_idx;
create index on test(a,b);
```

It takes longer time to create. Now, let's run previous queries again.

query - `explain analyze select c from test where a = 70;`

result - 

```sql
Bitmap Heap Scan on test  (cost=1254.23..69566.02 rows
=111200 width=4) (actual time=22.265..2860.315 rows=119
714 loops=1)
   Recheck Cond: (a = 70)
   Heap Blocks: exact=54535
   ->  Bitmap Index Scan on test_a_b_idx  (cost=0.00..1
226.43 rows=111200 width=0) (actual time=14.083..14.090
 rows=119714 loops=1)
         Index Cond: (a = 70)
 Planning Time: 0.260 ms
 Execution Time: 3855.188 ms
```

Here postgres uses bitmap scan using index 'test_a_b_idx'.

query - `explain analyze select c from test where b = 100;`

result - 

```sql
Gather  (cost=1000.00..129554.61 rows=11896 width=4) (
actual time=62.143..556.527 rows=12122 loops=1)
   Workers Planned: 2
   Workers Launched: 2
   ->  Parallel Seq Scan on test  (cost=0.00..127365.01
 rows=4957 width=4) (actual time=24.728..500.594 rows=4
041 loops=3)
         Filter: (b = 100)
         Rows Removed by Filter: 3995960
 Planning Time: 0.088 ms
 JIT:
   Functions: 12
   Options: Inlining false, Optimization false, Express
ions true, Deforming true
   Timing: Generation 10.372 ms, Inlining 0.000 ms, Opt
imization 10.974 ms, Emission 59.783 ms, Total 81.129 m
s
 Execution Time: 662.660 ms
```

Here is the interesting part begins - the above query did not use Bitmap scan but a full table scan. But why? We do have an index for b column.
Because, when we created the index we used (a,b) - that means a is on left side and b on right side of the btree. So, if we filter by a or (a & b), index will be used. For b, our composite index won't be used.

query - `explain analyze select c from test where a= 60 and b = 100;`

result - 

```sql
 Bitmap Heap Scan on test  (cost=5.57..437.46 rows=111 
width=4) (actual time=0.073..1.926 rows=100 loops=1)
   Recheck Cond: ((a = 60) AND (b = 100))
   Heap Blocks: exact=99
   ->  Bitmap Index Scan on test_a_b_idx  (cost=0.00..5
.54 rows=111 width=0) (actual time=0.036..0.045 rows=10
0 loops=1)
         Index Cond: ((a = 60) AND (b = 100))
 Planning Time: 0.078 ms
 Execution Time: 3.081 ms
```
And it is super fast. Because we created composite index and we used that index in full power in our where condition. So, we can get the benefit of composite index if we have condition like that.

But if we do the same query with `or` condition, result will be - 

```sql
Gather  (cost=1000.00..153203.61 rows=123386 width=4) 
(actual time=4.760..1168.125 rows=132298 loops=1)
   Workers Planned: 2
   Workers Launched: 2
   ->  Parallel Seq Scan on test  (cost=0.00..139865.01
 rows=51411 width=4) (actual time=4.846..713.851 rows=4
4099 loops=3)
         Filter: ((a = 60) OR (b = 100))
         Rows Removed by Filter: 3955901
 Planning Time: 0.084 ms
 JIT:
   Functions: 12
   Options: Inlining false, Optimization false, Express
ions true, Deforming true
   Timing: Generation 2.099 ms, Inlining 0.000 ms, Opti
mization 0.968 ms, Emission 12.820 ms, Total 15.887 ms
 Execution Time: 2264.472 ms
```

Postgres did a full table scan - because it is lot more cheaper than building a bitmap - as no index for finding the b.

Let's do the above query with a limit 1.

query - `explain analyze select c from test where a= 60 or b = 100 limit 1;`

result - 

```sql
Limit  (cost=0.00..1.98 rows=1 width=4) (actual time=0
.616..0.648 rows=1 loops=1)
   ->  Seq Scan on test  (cost=0.00..244865.01 rows=123
386 width=4) (actual time=0.597..0.606 rows=1 loops=1)
         Filter: ((a = 60) OR (b = 100))
         Rows Removed by Filter: 41
 Planning Time: 0.088 ms
 Execution Time: 0.705 ms
```

It is `seq scan` not even a `parallel scan`.

As we saw that we suffer when we tried to search for `b` as our index is not working for b. So, let's create one.
`create index on test(b);`

Now let's run the previous query again with just b.

query - `explain analyze select c from test where b = 150;`

result 

```sql
 Bitmap Heap Scan on test  (cost=136.63..30474.20 rows=
11896 width=4) (actual time=4.268..445.813 rows=12038 l
oops=1)
   Recheck Cond: (b = 150)
   Heap Blocks: exact=10974
   ->  Bitmap Index Scan on test_b_idx  (cost=0.00..133
.66 rows=11896 width=0) (actual time=2.296..2.303 rows=
12038 loops=1)
         Index Cond: (b = 150)
 Planning Time: 0.249 ms
 Execution Time: 545.343 ms
```

So, postgres finds our newly created index and used that.

query - `explain analyze select c from test where a= 60 and b = 100;`

It is fast like before as it uses `test_a_b_idx`.

Now, our slow query - `explain analyze select c from test where a= 60 or b = 100;`

result - 

```sql
 Bitmap Heap Scan on test  (cost=1424.78..68895.86 rows
=123386 width=4) (actual time=35.278..2581.900 rows=132
298 loops=1)
   Recheck Cond: ((a = 60) OR (b = 100))
   Heap Blocks: exact=56483
   ->  BitmapOr  (cost=1424.78..1424.78 rows=123496 wid
th=0) (actual time=24.599..24.639 rows=0 loops=1)
         ->  Bitmap Index Scan on test_a_b_idx  (cost=0
.00..1229.43 rows=111600 width=0) (actual time=23.802..
23.809 rows=120276 loops=1)
               Index Cond: (a = 60)
         ->  Bitmap Index Scan on test_b_idx  (cost=0.0
0..133.66 rows=11896 width=0) (actual time=0.768..0.776
 rows=12122 loops=1)
               Index Cond: (b = 100)
 Planning Time: 0.104 ms
 Execution Time: 3688.174 ms
```

Now, postgres finally uses `test_b_index` to find b and `test_a_b_idx` to find a and make a beautiful bitmap to fetch values.

**Note:** Index (btree) is not cheap. If index returns small value, then it worths searching the index (small values are scattered in db and made up the full db). But if index returns lots of values (95% values returned by index - so full table scan is better than index), then it is costly. For large values, then we need to cut down one index into multiple indexes.

**Statistics:** And database always keeps track of statistics of table. So, it can decide which index to use when. Or no index using at all. And here is an interesting story - Let's say we have a table with zero rows. So, database statistics has 0 stats. Now, we insert 1 - 2 - 3 rows. DB also updated its stats at just 3. So, if we now query something (let's say we have an index on that column), it will do full table scan as it is cheaper with just 3 rows. But now we insert  3 billion rows in bulk and query again. What is going to happen? DB still not updated its stats - it knows that it has only 3 rows - so it will do full table scan - where we have billion rows - so it gonna suck. So, if we ever do this kind of bulk insert we should do update the statistics with `vacuum or analyze` command in postgres. It is very subtle important thing.

But here is another interesting part - sometimes we can force database to use a specific index - like saying, 'It is my application and I know what to do. So do what I want.'
Here is the full [video](https://www.udemy.com/course/database-engines-crash-course/learn/lecture/25739154#content)
**Note:** In a production live DB, creating index is hard. Because there are multiple reads, writes are happening - so tables are locked. So, to solve this problem, postgres has another command `concurrently` associated with it.

`create index concurrently g on grades(g).` 

It will wait till all the locks are finished with reads and writes - then it will create the index. And we may have a little bit time, but it does not really matter at all.

**Bloom filter:** It is an interesting concept. Let's say we want to know something if a value exists or not in DB. So, what we do normally we search in db with a backend service. But db operation is expensive. So, to make it little bit faster, we can create something in memory. We can create a hashtable to store the values with hash index.

Let's say we create an bit array (0 or 1) of 64 length. And we are searching for name. So let's assume we have already built the array with name's hash index. So to search a name 'Java' -> hash(java) % 64 = 3 - if the value of index 3 is 1, then we might assume the value exists on db (it might not exist as other name might have same hash value) - so we can go to db and search for that. But if the index value is 0, then the name definitely not exists in db. So, it is kind of efficient thing to build and consider. But everything has a cost - let's say our db is huge and we have just 64 bit array - so it might be possible that every index in that array is 1 - so now searching for any name, we will get 1 and need to go to DB for assurance. To get rid of that, we may build our array larger enough so that we have some index with value 0.

Here is the [video](https://www.udemy.com/course/database-engines-crash-course/learn/lecture/22504032#content).

Note: In index, LSM tree data structure is also used.
**Working with Billion rows table:** Here is the [video](https://www.udemy.com/course/database-engines-crash-course/learn/lecture/22484500#content).

Let's say we are building twitter app and people need to be connected with each others. So, the `connection` table holds the A-B or B-C connections. So, it is gonna be huge table and will have billion rows in a short time. Here we will discuss some techniques to solve this problem.

- Do multi threading, multi-processing on the table and merge the result (like map-reduce)
- Indexing - where we search in a small data structure and then find all the rows.
- Partitioning - (Horizontal partitioning) We will break down the whole table into multiple partitions in a disk. To find which partition to use, `partition-key` is used. So, at first we will find the partition where data might be present, then index will help us to search in more narrow results. 
- Sharding - let's say we keep our partitions into multiple hosts. But now our system gets complicated. We have to deal with `transactions` and some other basic critical staff as now might be first 1000 customers in one db, next 1000 customers in other db. But now we cut off the billion rows table into thousands rows table by sharding -> partitioning - indexing.
- De-normalization - Let's say we have profile table. Here `follower's count` is a column `int`, another column `followers`, `json`. Now, we don't have another table to map the relation but a column where we will just add or remove follower from the json. So, the whole complexity we discussed before totally gone.

**The cost of Long Running Transactions: ** Let's say we have billion rows and our one update query effects few million rows - no, it failed and now rows are rolling back to previous version. So, what's gonna happen? How those dead rows should be removed from db?
Database should roll back as fast as possible - and if the rows contains index - the index should be updated. 
Postgres maintains versioning, so it moves the rows the previous version. 

There are few approaches database takes to fullfil this rollback.

- lazy approach - a kind of post processing.
- eager approach - lock the tables till the cleaning ends upto database restarts.

Postgres does the lazy approach where it immediately goes to the previous version and to remove the dead rows, it periodically does `vacuum` command to clean the junks.

But what is problem in this approach where millions of rows are already `dead`? Let's say we are fetching data (select query) from a page, the page contains 1000 rows where 999 rows are dead and 1 is alive. So, our db has a useless extra cost of IO operation which makes the select query slower.

Some other databases uses eager approach where it won't let us start the db before successful rollback. 

Whichever approach we take, our next transactions will not be affected by these dead rows. Transaction can check for the dead rows. So there is no risk of corrupted data but a slowness. 
Which approach is better? Both has pros and cons and very interesting concepts. So, we engineers make the decision which one to peek to solve a particular problem.

Here is a nice article to understand `index` from [MS SQL Server](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-index-design-guide?view=sql-server-ver15) 

### B Tree (not B minus) vs B+ Tree in Production Database Systems

#### Full table scan

- To find a row in a large table, we perform full table scan
- Reading large tables is slow
- Requires many I/Os to read all the pages
- We need a way to reduce this I/O for reading pages
- But doing a full table scan does not mean it is doing stupid - database does different tricks, like - using multiple treads to search from different section of a table etc.

#### B Tree

- Balanced data structures for fast traversal
- B tree has nodes
- In B tree of "m" degree some nodes can have (m) child nodes
- Node has up to (m-1) elements

But what is an element really?

- Each element has a key and a value
- The value is usually data pointer to the row
- Data pointer can point to primary key or tuple
- Root Node, internal node and leaf nodes
- A node = disk page

**Note:** Every table has its own tuple id (physical id kind of) connected to the row to identify the row. Let's say we have a primary index and now as it is b tree, the index and physical is made up in a B Tree. So, let's say we find a value 8:600 (primary key id:physical id), now we will go to 600 number row from where we will fetch all the values. But how to find 600 number row? Do I need to perform full table scan for this? Absolutely not! Database knows which page contains this 600 number row - so it will go there immediately without extra cost.

#### Limitation of B Tree

- Elements in all nodes store both the key and the value. But to find a particular item, we search in keys not value. So, our memory goes full very fast. But the main problem is - our nodes store two items now - key and value. So, one page can fit less nodes, so to find a particular item, database needs to go more pages (as few items in single page) - more page traverse means more I/O - so eventually it becomes slower. 
- Secondary index becomes very large - for B tree, it is very hard to fit the values in memory sometimes, like - UUID, String which could take a lot of space.
- Range query is slow for random access - like find all the values in range 1-5. But why? Because now our database will search for 1, at first in b tree, then 2 in b tree, then 3 in b tree. Interesting fact - in b -tree, all the items are sorted (kind of as like real B tree) but our database can not use it properly. It searches for every single item one by one. So, query becomes slower.

All the problems we see here are solved by B+ Tree (B plus tree). Let's talk about that.

#### B+ Tree (B Plus Tree)

Here is the video [link](https://www.udemy.com/course/database-engines-crash-course/learn/lecture/27467078#content).

- Exactly like B Tree but only stores keys in internal nodes
- Values are only stored in leaf nodes
- Internal nodes are smaller since they only store keys and can fit more elements - that means more items in a single page - so less I/o - so faster query
- Leaf nodes are `linked` so once we find a key we can find all values before and after that key - so great for range query.

#### B+ Tree & DBMS Considerations

- Cheap cost of leaf pointer as these are now linked. But MongoDB does use B+ Tree but without linked leaf pointer. Because, this link is useful for range query which is rare in MongoDB. The database designers took the decision that nobody is gonna do a range query in MongDB, so they did not implement in that way.
- 1 Node fits a DBMS page (most DBMS)
- Can fit internal nodes easily in memory for fast traversal
- Leaf nodes can live in data files in the heap
- Most DBMSs uses B+ tree.

#### Storage Cost in Postgres vs MySQL

- B+ Trees secondary index values can either point directly to the tuple (Postgres) or to the primary key (MySQL). 
- So if the primary key data type is expensive (UUID) this can cause bloat in all secondary indexes for databases like MySQL (InnoDB). But if the primary key is integer, then it is fine.
- Leaf nodes in MySQL(InnoDB) contains the full row since it is clustered index. So, all the columns are present in leaf.

### Partitioning 

Partitioning is an idea of splitting a single table into multiple ones. Let's say we have a `customer` table with 1M rows - so querying there for a particular customer id will be huge time consuming. Index can help a little but not properly. So, we can use the partition technique in that table - then we may have 5 tables, - `customer_200k` for first 200k customers, `customer_400k` for next 200k, and till to the 1M rows. So, our parent table `customer` does not have any rows at all - it just keeps tracking of its child partitioned databases. So, now if we do a query like finding id of 700, it will go to the 4th partition and give us the result very fast.

#### Vertical vs Horizontal Partitioning

- Horizontal partitioning split rows into partitions - range or list
- Vertical partitioning splits columns partitions - large column like - BLOB that we can store in a slow access drive in its own table space.

#### Partitioning types

- By range - dates, ids (logdate or customer id from to)
- By list - discrete value, like - states (CA, AL) or zip codes
- By hash (cassandra uses it) - consistent hashing

#### Horizontal Partitioning Vs Sharding

- HP splits big table into multiple smaller tables in the same database - and as a client (who does the query) it has no idea how many partitions are out there - it just runs query - underline database knows everything - so, we can call client is agnostic. 
- Sharding splits big table into multiple tables across different servers - yes those are completely other servers - let's say we have one shard for CA, one for NYC - ip address different, db different
- in HP, table name changes - yes, customer table is not customer_200k, customer_400k and so on. But in sharding, table name remains same as client (who runs the query) needs to hit in different ip, nothing else. So in sharding everything is the same but server changes.

#### Example

Let's prepare our workstation.

```sql
create table grades_org (id serial not null, g int not null);
insert into grades_org(g) select floor(random()*100) from generate_series(0, 10000000);
create index grades_org_idx on grades_org(g);
```
Let's run some query.

query - `explain analyze select count(*) from grades_org where g = 30;`

result - 

```sql
 Aggregate  (cost=2395.09..2395.11 rows=1 width=8) (act
ual time=1653.609..1653.639 rows=1 loops=1)
   ->  Index Only Scan using grades_org_idx on grades_o
rg  (cost=0.43..2139.26 rows=102333 width=0) (actual ti
me=0.036..835.043 rows=100037 loops=1)
         Index Cond: (g = 30)
         Heap Fetches: 0
 Planning Time: 0.131 ms
 Execution Time: 1653.716 ms
```

It is using our `Index Only Scan using grades_org_idx` because to get count it does not need to go to heap. 

query - `explain analyze select count(*) from grades_org where g between 30 and 35;`

result - 

```sql
 Finalize Aggregate  (cost=12234.48..12234.49 rows=1 wi
dth=8) (actual time=3117.014..3118.111 rows=1 loops=1)
   ->  Gather  (cost=12234.27..12234.48 rows=2 width=8)
 (actual time=3116.929..3118.044 rows=3 loops=1)
         Workers Planned: 2
         Workers Launched: 2
         ->  Partial Aggregate  (cost=11234.27..11234.2
8 rows=1 width=8) (actual time=3083.531..3083.560 rows=
1 loops=3)
               ->  Parallel Index Only Scan using grade
s_org_idx on grades_org  (cost=0.43..10605.10 rows=2516
67 width=0) (actual time=0.028..1549.669 rows=200192 lo
ops=3)
                     Index Cond: ((g >= 30) AND (g <= 3
5))
                     Heap Fetches: 0
 Planning Time: 0.197 ms
 Execution Time: 3118.184 ms
```

As expected, it used parallel scan with multiple worker threads.

Now, let's do some partitioning.

```sql
create table grades_parts (id serial not null, g int not null) partition by range(g);

create table g0035 (like grades_parts including indexes);

create table g3560 (like grades_parts including indexes);

create table g6080 (like grades_parts including indexes);

create table g80100 (like grades_parts including indexes);

-- let's attach those partitions

alter table grades_parts attach partition g0035 for values from (0) to (35);

alter table grades_parts attach partition g3560 for values from (35) to (60);

alter table grades_parts attach partition g6080 for values from (60) to (80);

alter table grades_parts attach partition g80100 for values from (80) to (100);

\d g0035;
\d g80100;

insert into grades_parts select * from grades_org;

```
Now the fun begins. 

query - `select count(g) from grades_parts;` it gives the 10000001.

query - `select max(g) from g0035;` - 34
`select count(*) from g0035;` - 3498565
`select max(g) from g3560;` - 59
`select count(*) from g3560;` - 2500772

The leader table is nothing but a virtual table.
Let's create an index on the leader table which will eventually create index on the partitions. (This feature might come at Postgres 11, prior that we needed to create index on all the partitions)

```sql
create index grades_parts_idx on grades_parts(g);
\d grades_parts;
\d g0035;
```

We will see that index is also created on the partitions tables automatically.

query - `explain analyze select count(*) from grades_parts where g=30;`

```sql
Aggregate  (cost=2300.29..2300.30 rows=1 width=8) (act
ual time=1644.660..1644.689 rows=1 loops=1)
   ->  Index Only Scan using g0035_g_idx on g0035 grade
s_parts  (cost=0.43..2054.81 rows=98193 width=0) (actua
l time=0.047..830.990 rows=100037 loops=1)
         Index Cond: (g = 30)
         Heap Fetches: 0
 Planning Time: 0.320 ms
 Execution Time: 1644.759 ms
```

Interestingly if we run the exact query for original non partitioned table, we will see almost same result.

query - `explain analyze select count(*) from grades_org where g=30;`

result-

```sql
 Aggregate  (cost=2603.09..2603.11 rows=1 width=8) (act
ual time=1664.145..1664.174 rows=1 loops=1)
   ->  Index Only Scan using grades_org_idx on grades_o
rg  (cost=0.43..2347.26 rows=102333 width=0) (actual ti
me=1.584..843.437 rows=100037 loops=1)
         Index Cond: (g = 30)
         Heap Fetches: 0
 Planning Time: 0.097 ms
 Execution Time: 1664.242 ms
```

So, we may wonder - partition does not help very much. But not really. 
Because - here we are using docker container which has not limit - it uses all the computer's power. If we could bound our docker in 500MB or some limited memory usage, then we will see that our `huge index` is not fitting into memory - then we will see that our smaller index in partition is gonna be very fast compared to the large index without partitioned table.

Let's see the index size - 

query - `select pg_relation_size(oid), relname from pg_class order by pg_relation_size(oid) desc;`

result -

```sql
110403584 | grades_org_id
24281088 | g0035_g_idx
17367040 | g3560_g_idx
13901824 | g80100_g_idx
13893632 | g6080_g_idx
```

we see that grades_index is larger than other indexes. 

query - `show ENABLE_PARTITION_PRUNING;`

We must enable this feature `ON`. Otherwise database will hit all the index.

```sql
set ENABLE_PARTITION_PRUNING = off;
explain analyze select count(*) from grades_parts where g=30;
```

result - 

```sql
 Aggregate  (cost=2804.62..2804.63 rows=1 width=8) (act
ual time=3208.269..3208.366 rows=1 loops=1)
   ->  Append  (cost=0.43..2559.13 rows=98196 width=0) 
(actual time=0.079..2404.268 rows=100037 loops=1)
         ->  Index Only Scan using g0035_g_idx on g0035
 grades_parts_1  (cost=0.43..2054.81 rows=98193 width=0
) (actual time=0.053..827.437 rows=100037 loops=1)
               Index Cond: (g = 30)
               Heap Fetches: 0
         ->  Index Only Scan using g3560_g_idx on g3560
 grades_parts_2  (cost=0.43..4.45 rows=1 width=0) (actu
al time=0.078..0.086 rows=0 loops=1)
               Index Cond: (g = 30)
               Heap Fetches: 0
         ->  Index Only Scan using g6080_g_idx on g6080
 grades_parts_3  (cost=0.43..4.45 rows=1 width=0) (actu
al time=0.027..0.045 rows=0 loops=1)
               Index Cond: (g = 30)
               Heap Fetches: 0
         ->  Index Only Scan using g80100_g_idx on g801
00 grades_parts_4  (cost=0.43..4.45 rows=1 width=0) (ac
tual time=0.039..0.046 rows=0 loops=1)
               Index Cond: (g = 30)
               Heap Fetches: 0
 Planning Time: 0.669 ms
 Execution Time: 3208.459 ms
```

Out whole partitioning is useless now. So we must always enable it. It is enabled by default.

**Note:** Default page size of postgres is 8KB and MySQL is 16KB. In MySQL, it is possible to point a table to a CSV file (as a storage engine)

### Pros of Partitioning 

- Improves query performance accessing a single partition
- Sequential scan vs scattered index scan (going to index and then heap - doing this back and forth). So, which scan is better - this decision is made by query planner. So if we have large index or table it is hard to decide but in a single partition it is easier.
- Easy bulk loading that means we can attach partition immediately to the parent table
- We can archive old partition which is rarely accessed, in a cheap storage. 

### Cons of Partitioning

- Updates like - insert, update, delete are slow - and if the updates that move rows from a partition to another - become slower - it also fail sometimes
- Inefficient queries could accidentally scan all partitions - becomes slower. Let's say we are querying something which results 95% of the table - but now instead of one single table, it is scanning all the partitions which are 5/7 tables.
- Schema changes are challenging - like adding index or something else. But most of the time DBMS manage it though