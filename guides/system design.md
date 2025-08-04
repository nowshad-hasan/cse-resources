## Contents

- [Contents](#contents)
- [Core](#core)
  - [Articles](#articles)
  - [Course](#course)
  - [Collection](#collection)
  - [Others](#others)
  - [Videos](#videos)
  - [Discussion](#discussion)
  - [Github Repo](#github-repo)

## Core
* HTTP Caching [Roadmap](https://roadmap.sh/guides/http-caching)
* Scaling Databases [Roadmap](https://roadmap.sh/guides/scaling-databases)

### Articles

* Autocomplete Search [System Design Interview Preparation](https://systemdesignprep.com/autocomplete)
* Crack the System Design interview: tips from a Twitter software engineer [Geeksforgeeks](https://www.freecodecamp.org/news/how-to-system-design-dda63ed27e26/)
* Announcing Snowflake [Twitter](https://blog.x.com/engineering/en_us/a/2010/announcing-snowflake)
* Data Structures & Algorithms I Used Working at Tech Companies [Pragmatic Engineer](https://blog.pragmaticengineer.com/data-structures-and-algorithms-i-actually-used-day-to-day/)
* Strategy Design Pattern Made Easy! [Linkedin](https://www.linkedin.com/pulse/strategy-design-pattern-made-easy-mahmudul-hasan-rafi-7owgc/)
* What is fault tolerance, and how to build fault-tolerant systems [Cockroach Labs](https://www.cockroachlabs.com/blog/what-is-fault-tolerance/)
* What’s the Difference Between Throughput and Latency? [Amazon AWS](https://aws.amazon.com/compare/the-difference-between-throughput-and-latency/)
* Acid Transactions [Redis](https://redis.io/glossary/acid-transactions/)

### Course
* System Design [HIRED IN TECH](https://www.hiredintech.com/system-design/introduction/)

### Collection

* System Design by Gaurav Sen [YouTube Playlist](https://www.youtube.com/playlist?list=PLMCXHnjXnTnvo6alSjVkgxV-VH6EPyvoX)
* [High Scalability](http://highscalability.com/)
* High Scalability Notes [GitHub Project](https://github.com/mgp/book-notes/blob/master/high-scalability-notes.markdown)
* The System Design Primer [GitHub Repo](https://github.com/donnemartin/system-design-primer)
* Basics of System Design [Coding Simplified](https://www.youtube.com/playlist?list=PLt4nG7RVVk1g_LutiJ8_LvE914rIE5z4u)
* System Design [Tech Dummies](https://www.youtube.com/playlist?list=PLkQkbY7JNJuBoTemzQfjym0sqbOHt5fnV)
* Grokking the System Design Interview [Educative.io](https://www.educative.io/courses/grokking-the-system-design-interview)
* SYSTEM DESIGN PREPARATION [shashank88-Github](https://github.com/shashank88/system_design)

### Others

* How To Become a DevOps Engineer In Six Months or Less [Medium](https://medium.com/@devfire/how-to-become-a-devops-engineer-in-six-months-or-less-366097df7737)

### Videos

- Bloom Filters Explained by Example [Hussein Nusser](https://youtu.be/gBygn3cVP80?si=3KnNyMvjsvKss0PI) 
- সিস্টেম ডিজাইন ইন্টারভিউ - কীভাবে একটি ওয়েব অ্যাপ্লিকেশনকে স্কেল করতে হয় [Tamim Shahriar Subeen](https://youtu.be/UDadRJO5iec?si=gknzJa1E9OXJH2vt)
- How Slack Works [infoQ](https://youtu.be/WE9c9AZe-DY?si=DXSYrxe2COwXn-9q)
- 10 YouTube Backend, Protocols, Networking, Database Channels to Follow in 2021 (I watch them all) [Hussein Nasser-Youtube](https://youtu.be/eusHw-mUa8Y?si=XBoyhBvW87d_eQRx)
- Mastering Chaos - A Netflix Guide to Microservices [infoQ-Youtube](https://youtu.be/CZ3wIuvmHeM?si=Qf2_L83Bf7wh6Yp9)
- Critical rendering path - Crash course on web performance (Fluent 2013) [Youtube](https://youtu.be/PkOBnYxqj3k?si=RlvllMUfXoLIe0GD)
- System Design Interview Question: DESIGN A PARKING LOT - asked at Google, Facebook [Youtube](https://youtu.be/DSGsa0pu8-k?si=IuYLzAQnJu7oARUW)


### Discussion

Q1. You're in a backend interview.

They ask:
"Design a rate limiting system for an API used by millions. Where do you start?"

Here’s how you impress 👇

1.Start with the goal:
Prevent abuse, ensure fair usage, and protect system stability.


2. Choose a rate-limiting strategy:
 - Fixed window
 - Sliding window
 - Token bucket (most flexible for millions of users)
 - Leaky bucket

3. Decide on the granularity:
 - Per user? Per IP? Per API key?
 - Define the limit (e.g. 100 req/min)

4. Pick a fast, distributed store:
 Use Redis to track usage—fast reads/writes, with key expiry support.

5. Implementation flow:
 - Each request checks Redis.
 - If within limit → continue.
 - Else → return 429 Too Many Requests.

6. Make it scalable:
 - Use Redis clusters for horizontal scaling.
 - Hash keys for sharding.
 - Push logic to API gateways like Kong or Cloudflare Workers for global edge enforcement.

7. Add burst control and global override flags:

### Github Repo

- [System Design Playbook](https://github.com/systemdesign42/system-design)
- [awesome-system-design-resources](https://github.com/ashishps1/awesome-system-design-resources)
- [system-design-primer](https://github.com/donnemartin/system-design-primer)
- [system-design-101 by ByteByteGo](https://github.com/ByteByteGoHq/system-design-101)
- [awesome-system-design](https://github.com/madd86/awesome-system-design)
- [awesome-front-end-system-design](https://github.com/greatfrontend/awesome-front-end-system-design)