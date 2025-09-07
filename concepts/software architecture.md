### Articles

- Best Practices for Designing a Pragmatic RESTful API [Vinay Sahni](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
- Inline Documentation for RESTful web APIs [APIDOC](https://apidocjs.com/)
- The Uber Engineering Tech Stack, Part I: The Foundation [Uber Engineering](https://www.uber.com/en-BD/blog/tech-stack-part-one-foundation/)
- How to design a system to scale to your first 100 million users [Medium](https://levelup.gitconnected.com/how-to-design-a-system-to-scale-to-your-first-100-million-users-4450a2f9703d)
- API Architecture - Design Best Practices for REST APIs [Medium](https://blog.wahab2.com/api-architecture-best-practices-for-designing-rest-apis-bf907025f5f)
- Eventual vs Strong Consistency in Distributed Databases [Hackernoon](https://hackernoon.com/eventual-vs-strong-consistency-in-distributed-databases-282fdad37cf7)
- What is distributed tracing, and why is it important? [Dynatrace](https://www.dynatrace.com/news/blog/what-is-distributed-tracing/)
- Synchronous vs. asynchronous communications: The differences [Blog](https://www.techtarget.com/searchapparchitecture/tip/Synchronous-vs-asynchronous-communication-The-differences)
- Batch Processing vs Stream Processing: Key Differences Explained [Atlan](https://atlan.com/batch-processing-vs-stream-processing/)
- REST API Design Best Practices [Medium](https://medium.com/@techworldwithmilan/rest-api-design-best-practices-2eb5e749d428)
- 3 Questions to define a software product [KAZ Software](https://kaz.com.bd/blog/2020/1/2/3-questions-to-define-a-software-product)
- 7 best practices to know about before you outsource software [KAZ Software](https://kaz.com.bd/blog/2016/10/9/best-practices-for-software-development-outsourcing)
- 10 Steps to Making Your Software Product [KAZ Software](https://kaz.com.bd/blog/2020/7/7/making-your-software-defining-what-the-software-will-do-wireframes-and-mockups)
- Making your software: Step 1 - A rough specification [KAZ Software](https://kaz.com.bd/blog/2020/7/20/making-your-software-step-1-a-rough-specification)

### Talks and Conferences

- A Philosophy of Software Design | John Ousterhout | Talks at Google [Talks at Google-Youtube](https://youtu.be/bmSAYlu0NcY?si=tyQR_eZUeXxjO2SL)
- Core Design Principles for Software Developers by Venkat Subramaniam [Devoxx](https://youtu.be/llGgO74uXMI?si=cYodM4ODVT7vdIvK)
- "The Early Days of id Software: Programming Principles" by John Romero (Strange Loop 2022) [Youtube](https://youtu.be/IzqdZAYcwfY?si=nA-m4ztXdeeGwfxD)

### Scalability
- How Grab configured their data layer to handle multi-million database transactions a day! [Arpit Bhayani-Youtube](https://youtu.be/KeV4erIm47o?si=wPuz0yO9sMgtbbm6)
- The Business of Building Apps - App Product Management Course for Developers [Freecodecamp-Youtube](https://youtu.be/poLzjLt2yqU?si=-0r2Fbk81HcIY4_u)
- (Bangla) Software architecture 01 : What should we consider when designing a software from scratch [Foyzul Karim](https://youtu.be/HDEWoUcHCqA?si=t_vjingKHp8zqi_n)
- Building Software Systems At Google and Lessons Learned [Stanford-Youtube](https://youtu.be/modXC5IWTJI?si=ujXwrMtTvxAHSuA0)

### Courses
- Information​ ​Systems Specialization [Coursera](https://www.coursera.org/specializations/information-systems)


### Videos
- Software Design by Jakob Jenkov [Youtube playlist](https://youtube.com/playlist?list=PLL8woMHwr36HWMaOTIiWNf_fYYkZCHPxo&si=7h1b5xi8PdOCOJq2)
- Worldwide Software Architecture Summit by Geekle Official [Youtube playlist](https://youtube.com/playlist?list=PLnQIVBzL8WX72gAXkrLLDq3ovFR4CDugP&si=hs4VLmkNc4QYQYkB)

### Discussions

🔍 Decoding common API Endpoint Formats:
--------------------------------------------- 
- Singular vs. Plural Nomenclature:
🚀 Format: Choose between singular and plural endpoint names. Example: /user (singular) vs. /users (plural)

- Resource Nesting:
🌐 Format: Employ nesting for hierarchical resources.Example: /organizations/{orgId}/users/{userId}

- Action-Oriented Verbs:
⚡ Format: Use action-oriented verbs for specific operations.Example: /create-account, /update-profile

🌟 Recommended Endpoint Naming Strategy:
----------------------------------------------
- 🗂️ Resource-Centric Clarity:
🌐 Strategy: Prioritize clarity and consistency with resource-centric naming.Example: /articles for articles resource, /comments for comments resource

- 🚀 Actionable and Predictable:
⚡ Strategy: Ensure endpoints are actionable and predictable.Example: /approve-request, /list-orders

- 🎯 RESTful Conventions:
📄 Strategy: Embrace RESTful conventions for simplicity.Example: /users/{userId}/posts/{postId}


When I am building any API, these are the top 4 things I try to always consider regardless of the language or framework (or requirements):

1. Request validation - Query, path or body json parameters - all should be validated through some standard schema or validation mechanism supported by your framework for consistent checking and error messages. This is not only for the request layer, but internally while implementing business logic, it's important to validate nulls or undefined values.

2. Security - Usually authentication and authorization are handled by middleware, but sometimes business logic dictates the permission level of the API. For example, you have to query some data and return only the relevant data filtered by the user ID of the request (authenticated) user, on top of checking authorization. (AKA row level auth)

3. Error handling - Unhandled exceptions should be managed through some central error handler in your framework, but otherwise business logic specific error handling should be considered. For those scenarios having custom exceptions kept in a central location can help manage all the possible exceptions your system can throw. 

4. Logging - Request and response logging can be done at the framework level with some middleware (with some request ID for tracing). For API specific logging or error tracing, it's important to log key information that can help identify specific errors or steps in the logic. If possible include the request ID as a log prefix (this can also be done from a framework level).

<!--  -->

If your system calls any third party API, there are a few things you need to keep in mind.

1. Latency: Overall latency of your process will increase. Knowing the average latency of the 3rd party will help you decide whether the new total latency is acceptable or not. Otherwise you might need to consider alternatives in terms of provider or architecture (e.g. async).

2. Error Handling: Always assume that your dependencies will fail, and act accordingly. Handle errors gracefully for a better user experience. Think about retry mechanisms, and how that affects the latency as well. Consider different types of errors, e.g. network errors, timeouts, internal errors, etc. Also think about implementing a circuit breaker either through the application or through your infrastructure.

3. Separation of Concerns: Keep third-party API calling logic in a separate service class or function. This way you can declare interfaces and write unit tests with mock dependencies. This also allows you to configure the API caller specific to that API, for example you can implement a retry mechanism there itself, and also configure retry count, timeouts, etc.

4. Schema: Align your interfaces / types with the 3rd party API documentation, or at least make sure you have typed interfaces for the API so that you know the inputs, outputs and errors during development.

5. Configuration: You should store the base URL of the API and any authentication details e.g. tokens in any kind of config management mechanism, e.g. through environment variables.

6. Outbox: If you need to keep track of external API calls and ensure whether or not they are successfully called, especially in an asynchronous system, you might want to consider an outbox pattern to keep track of successes, failures, and other metadata.

Edit: Forgot to mention logging. Application level logs of important parameters and response details can help with debugging.

<!--  -->

When I am building any API, these are the top 4 things I try to always consider regardless of the language or framework (or requirements):

1. Request validation - Query, path or body json parameters - all should be validated through some standard schema or validation mechanism supported by your framework for consistent checking and error messages. This is not only for the request layer, but internally while implementing business logic, it's important to validate nulls or undefined values.

2. Security - Usually authentication and authorization are handled by middleware, but sometimes business logic dictates the permission level of the API. For example, you have to query some data and return only the relevant data filtered by the user ID of the request (authenticated) user, on top of checking authorization. (AKA row level auth)

3. Error handling - Unhandled exceptions should be managed through some central error handler in your framework, but otherwise business logic specific error handling should be considered. For those scenarios having custom exceptions kept in a central location can help manage all the possible exceptions your system can throw. 

4. Logging - Request and response logging can be done at the framework level with some middleware (with some request ID for tracing). For API specific logging or error tracing, it's important to log key information that can help identify specific errors or steps in the logic. If possible include the request ID as a log prefix (this can also be done from a framework level).

There are a bunch more things to keep in mind like proper status codes, caching, rate limiting, documentation etc some of which are handled by the framework by default, and others depending on the scenario.

<!--  -->

Regardless of which API framework or language you use, there are some key concepts that are crucial for any production grade web APIs, such as:

- Request (data) validation
- Security (authentication / authorization)
- Controller / view / business logic separation
- Exception handling (centralized + business logic wise)
- Logging (good for monitoring and debugging)
- Standard and consistent response formats and status codes
- Modular and intuitive routes

<!--  -->

Most effective software engineers that I know have this skill:

Breaking down a problem into smaller pieces

Here are examples of how this applies across multiple roles in software engineering:

→ Frontend engineers break down web pages into reusable components.

→ Backend engineers break down feature/API requirements into components like database schema, API urls, validations, business logic, etc.

→ QA engineers break down requirements into test cases / reproduceable flows.

→ Tech leads or software architects break down complex requirements in a system or architecture level diagram.

→ Team leads or engineering managers break down epics into smaller, achievable deliverables.

Bonus: Product Owners break down requirements into user stories.

Mastering this skill will accelerate your problem-solving abilities, and enable your growth.

<!--  -->

We try to maintain 95%+ code coverage in our codebase. What does this actually mean? And how can we ensure this?

Code coverage can be broken down into four components - statements, branches, functions, and lines.

𝗙𝘂𝗻𝗰𝘁𝗶𝗼𝗻 𝗰𝗼𝘃𝗲𝗿𝗮𝗴𝗲 - ensures that each function in the codebase been called at least once

𝗦𝘁𝗮𝘁𝗲𝗺𝗲𝗻𝘁 𝗰𝗼𝘃𝗲𝗿𝗮𝗴𝗲 - ensures each statement in the code has been executed at least once

𝗕𝗿𝗮𝗻𝗰𝗵 𝗰𝗼𝘃𝗲𝗿𝗮𝗴𝗲 - ensures each branch (e.g. if/else/switch) been executed. E.g. for an if/statement, whether both the true and false cases have been covered

𝗟𝗶𝗻𝗲 𝗰𝗼𝘃𝗲𝗿𝗮𝗴𝗲 - ensures each executable line in the source code has been executed

(The above 4 are actually deduced from the Jest coverage output).

Now, when someone raises a PR, we can have tools like sonarcloud to do static analysis and code coverage analysis as well, but sonarcloud thresholds can still pass while missing some edge cases.

At first, we had team members post screenshots of their code coverage results in the PR comment, but that was tedious.

(Note: codebase is using NestJS)

So I ended up writing a script to parse the coverage output from Jest, and compare it with the diff of files between the PR base branch and the current one, and create a comment in the Github PR itself, all through our Github action for PRs.

Included the coverage-diff.js script and the sample Github action yml in the comments!

Edit: Test coverage and code coverage are different things. Code coverage helps you verify if each line of code is being executed by existing tests or not, and test coverage indicates whether those tests are covering all the functional requirements of the application or not. So a low code coverage can mean buggy code, but a high code coverage doesn't necessarily mean there won't be any bugs. Thanks to Wayne Roseberry for pointing it out!



<!--  -->

One tip to set yourself apart as a software engineer is to know the why behind the how and what.

Whatever technology or stack you are working with, try to understand why it is used, what impact it has, the tradeoffs of alternatives.

For example, if you have experience working with MongoDB then that's good. What would be better if you can explain:
1. Why MongoDB was chosen for this project specifically?
2. What benefits do MongoDB provide for this project?
3. What would be the pros/cons of using alternatives?
4. What are some interesting features of MongoDB (that you may or may not be using)?

Staying within your scope of work is good, having endless curiosity to learn is better.

<!--  -->

Primary Bottlenecks for Scalability of a Software System:

1. Application design: A poorly designed application’s architecture can become a major bottleneck as a whole. A typical architectural mistake is not using asynchronous processes and modules wherever required, rather, all the processes are scheduled sequentially. For example, if a user uploads a document on the portal, tasks such as sending a confirmation email to the user, and sending a notification to all subscribers/listeners to the upload event should be done asynchronously. Tasks like these should be forwarded to a messaging server or a task queue for asynchronous processing as opposed to being processed sequentially, making the user wait.

2. Adding business logic to the database: No matter what justification anyone provides, I’ve never been a fan of adding business logic to the database. The database is just not the place to put business logic. Business logic in the database makes the application components tightly coupled. Imagine how much code refactoring this would require when migrating to a different database. Also, the testing gets complex.

3. Database Scaling: Just think, we have an application that appears to be well architected. Everything looks good. The workload runs on multiple nodes and it can scale horizontally. However, the database is a poor single monolith where just one server has the onus of handling the data requests from all the server nodes of the workload. This scenario is a bottleneck. The server nodes work well, and handle millions of requests at a point in time efficiently, yet, the response time of these requests and the latency of the application are abysmal due to the presence of a single database. 

So the database needs to be scaled well. You should make wise use of database partitioning, and sharding with multiple database servers to make your system efficient.

4. Not picking the right database: Picking the right database technology is vital for businesses. If you need transactions and strong consistency pick a relational database. If you can do without strong consistency rather than need horizontal scalability then pick a NoSQL database.

Trying to pull things off with a not-so-suitable tech always has a profound impact on the latency of the entire application in negative ways. 

5. At the code level: This shouldn’t come as a surprise but inefficient and poorly written code has the potential to bring down the entire service in production. 

This typically includes:
 ** Using unnecessary loops or nested loops
 ** Writing tightly coupled code
 ** Not paying attention to the Big-O complexity while writing the code. (be ready to do a lot of firefighting in production)

Ideally, we should always do a DENTTAL (Documentation, Exception Handling, Null pointers, Time complexity, Test coverage, Analysis of code complexity, Logging) check of our code when doing a dry run.


<!-- API Documentation -->

Naming Convention ফলো করা, Data validation নিশ্চিত করা, HTTP Status code, Database Seeding, Endpoint Planning, Endpoint Testing, Debugging, Pagination, Sending Correct Error Messege, Middleware লেখা, Decorator লেখা, CORS, Unnecessary data hiding, Table id hiding, Authentication and Authorization, OAuth, WebHooks Security, Token, Permission, Filtering, HATEOAS ইউজ করা, API Versioning. এছাড়াও HTTP method overriding allow করা, Verb নয়, সবসময় Noun ইউজ করা, সবজায়গায় Consistency