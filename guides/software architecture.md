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

### Talks and Conferences

- A Philosophy of Software Design | John Ousterhout | Talks at Google [Talks at Google-Youtube](https://youtu.be/bmSAYlu0NcY?si=tyQR_eZUeXxjO2SL)
- Core Design Principles for Software Developers by Venkat Subramaniam [Devoxx](https://youtu.be/llGgO74uXMI?si=cYodM4ODVT7vdIvK)
- "The Early Days of id Software: Programming Principles" by John Romero (Strange Loop 2022) [Youtube](https://youtu.be/IzqdZAYcwfY?si=nA-m4ztXdeeGwfxD)

### Scalability
- How Grab configured their data layer to handle multi-million database transactions a day! [Arpit Bhayani-Youtube](https://youtu.be/KeV4erIm47o?si=wPuz0yO9sMgtbbm6)
- The Business of Building Apps - App Product Management Course for Developers [Freecodecamp-Youtube](https://youtu.be/poLzjLt2yqU?si=-0r2Fbk81HcIY4_u)
- (Bangla) Software architecture 01 : What should we consider when designing a software from scratch [Foyzul Karim](https://youtu.be/HDEWoUcHCqA?si=t_vjingKHp8zqi_n)

### Courses
- Information​ ​Systems Specialization [Coursera](https://www.coursera.org/specializations/information-systems)


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