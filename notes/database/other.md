SQl vs NoSQL

- SQL is relational. That means it enforces strong data consistancy. but NoSQL is not-relational
- SQL is vertially scalable. Though by partitionaing, sharding SQL is also horizontally scalable where master-slave architecture is applied. NoSQL is by nature vertically scalable with as needed partitions.
- SQL is table based structure. NoSQL is documents / key-value / graph based structure. And it is unstructured JSON.

- NoSQL is built to scale with high performace.

When to use what

- SQL is used when our access patterns are not defined. NoSQL is used when our access pattern is defined.

- SQL is used when to perform flexible queries, relational queries. And we want to enforce field constraints
- NoSQL is used when we need high scale, high performance and low latency

Use case:
1. Let's say we have a e-commerce app. So, the order of an user can be managed by SQL, where the user's session, log and other unstructured data can be saved in NoSQL.

When to choose right database by ByteByteGo (Kind of migrating from old db)

1. We should read the manual of our live production db again and again to know some tweaks about the existing db. With that tuning we can give more breath to the existing db.
2. We can use advance techniques like, partitioning, sharding, add more replicas to the existing db so that we don't change the existing db. We can also ask for help to the community if anyone can help for our problem. 
3. If we still fail to solve our problems then we can move to thinking migrating our db which is risky, time-consuming and could be error-prone.
4. For the new db, we need to read the manual of that db very carefully so that we can know details about that and we don't need to change again after few months. We need to read the section FAQ, Limitations and other critical pages carefully so that we know the limitations of that DB.
5. We need to measure the benchmark of that new db comapred to the existing db. And we should test the db performance with real world testing scenario not just few test cases. Our experimental db will show the true nature when testing the real scenario. This testing could let us months but it is necessary
6. If we are convinced with new db, then we should start migrating a single service into production. If that work fine, then we can think of migrating the full application but that could take months or years
