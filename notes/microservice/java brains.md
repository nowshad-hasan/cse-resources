Here is the playlist of this notes https://www.youtube.com/@Java.Brains/playlists

### Part 1

We will depart a monolithik application into multiple microservices and lets them talk. And that is simplistic thing we will ever do.
There are some challenges - 

- Lots of technologies
- Lots of patterns
- Interdependent concepts

What are the benefits we get with microsevices?

- Scalability
- Modularity of deployment
- Faster and independent release cycle
- Multiple node deployment for scalability

Some common problems for microsevce, like - 
- Load balancing

Microservice vs service oriented architectures
Service oriented architectures (web services) were used in early 2000 where these are used as like of utility and it was re-usable. Like - ip locator service, it was built by someone, and many people used it to know the location of an ip. But microservice is built for ourselves and for a particular target. And it can be re-used.
Service oriented architecture is very strict contract because who know who is gonna use it. It was implemented using SOAP and it is also very strict. People making these services tried to standardised it.

What we will build?

Movie catalog Service 
Input - UserID
Output - Movie ID and ratings

1. Movie Info Service
    Input - MovieId
    Output - movie details
2. Ratings Data Service
    Input - UserID
    Output - Movie Id and ratings
3. Movie catalog service - main entry point of our application
   Input - userId
   Output - ratings, movie name and everything

So, we built MovieCatalogService application with just `Web` dependency where url is - 

http://localhost:8081/catalog/{anything}
 gives 
 ```json
[{"name":"Transformers","desc":"Fiction","rating":4}]
 ```

 http://localhost:8082/movies/{movieId}
 
 ```json
{"movieId":"{movieId}","name":"Transformers"}
 ```

 http://localhost:8083/ratingsdata/movies/{movieId}

 ```json
{"movieId":"asdf","rating":4}
 ```

Now, from movie-catalog service we will movie-info service to get movie information. We will use RestTemplate for that data.

Note: As we are fetching data from movie-info-service, it is better to have that Movie class in movie-catalog service also for deserialization. But is not that bad to copy all the model classes in microservice? No! Because for smooth data transfer it is necessary. Yes, we can manage separate library for managing these model classes, but then we are stuck like monolithic application. The library needs to keep updated always, and our microservice project will be paused or broken for that library's life cycle. That is not the purpose of microservice. But we can copy-paste that classes from other projects but can remove unnecessary fields from that and use what we need.

So far what we did, is calling rating-info service to get the ratings at first with RestTemplate, then with a loop of ratings object, get the movieId from the rating object, get the movie info with another API call from movie-info service. All these done from movie-catalog-service with a basic RestTemplate (For async feature, we should use WebClient, though it is also possible to be synchronised in there also).

But what we did wrong here is hardcoded url in movie-catalog-service. Why?

- changes requires code updates
- Dynamic URLs in the cloud, we can hardcode localhost, but for a real IP, it would break
- Load balancing, so multiple instances are there 
- Multiple environments

So, we can add `Service Discovery` to solve this problem. But there are two models for this.
`Client side service-discovery`: Client will first communicate with discovery to find the url of a service, get the url as response, then calls the url of the service again to pass its messages.

`Server side service-discovery`: It will directly call the service-discovery with its message and required service id, then service-discovery will pass its message to destines service and response back to the client again.

Both models have own pros and cons.
But spring cloud uses client side discovery.

We will use Eureka as service discovery created by Netflix. It has other open source projects also - 
1. Eureka
2. Ribbon
3. Hysterix
4. Zuul

But we don't have to mess with this projects because Spring add an abstraction layer above these projects, like JPA and Hibernate. So, we will do coding in Spring and it will handle how to communicate with these projects itself.

Eureka will work as - Eureka Client and Eureka Server. Eureka server will have the registration of our microservices. And Eureka client will be top of Eureka server, it will talk to Eureka server when someone knocks it for data passing.

There are two dependencies for Eureka - 

1. Eureka Server - to make a spring boot project as Eureka Server
2. Eureka Client - To register a microservice as discoverable by Eureka Server.

So, we are gonna start a separate spring boot project with just `org.springframework.cloud:spring-cloud-starter-netflix-eureka-server` dependency. I'm just pasting the whole configuration file here - 

```groovy
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.0.5'
	id 'io.spring.dependency-management' version '1.1.0'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

repositories {
	mavenCentral()
	maven { url 'https://artifactory-oss.prod.netflix.net/artifactory/maven-oss-candidates' }
}

ext {
	set('springCloudVersion', "2022.0.2")
}

dependencies {
	implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-server'
	implementation 'javax.xml.bind:jaxb-api:2.3.1'
	implementation 'com.sun.xml.bind:jaxb-impl:2.3.1'
	implementation 'org.glassfish.jaxb:jaxb-runtime:2.3.1'
	implementation 'javax.activation:activation:1.1.1'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

tasks.named('test') {
	useJUnitPlatform()
}
```

The `jaxb` dependencies could be not necessary.
But still we need to below lines in application.properties file.

```properties
server.port=8761

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

Because, every eureka server is also a eureka client, so our server is trying to communicate with other eureka server, that's why we are turning those off.
We need to add `@EnableEurekaServer` annotation in the main class where `@SpringBootApplication` resides. 
Now, if we restart our server and access it by localhost:8761, we will see a home page but with no registered eureka client. For that, we need to add `	implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'` dependency and rest of things like dependencymanagement, spring cloud version etc in build.gradle of our 3 microservices. And nothing else! Now, if we restart our microservices we will see that in service-discovery homepage. 
Just adding the eureka-client dependency in the classpath is enough to be discovered by eureka server. How amazing!

Now, let's add name to our services so that they can find each other when needed.
Add the line below in application.properties file 
```
spring.application.name = rating-data-service
```

If we now restart the microservice, we will see in our service-discovery homepage that this name comes up.
Now, let's add `@LoadBalanced` annotation in our RestTemplate bean configuration in movie-catalog-service. 

```java
 @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
```

Now, rest template can now go to service-discovery and find other microservice easily. But we need to change the hardcoded url from calling those services. We can change like below - 
`http://localhost:8083/ratingsdata/user/` can be replaced with `http://ratings-data-service/ratingsdata/user/`. Kind of interesting, but very meaningful and logical.

But here is the point. It should work like before but without hardcoded url. And it is using client-side-service-discovery that means innerly our microservice add a hit to service-discovery and get the actual url, then again calls to the actual server. So, 2 api calls are made but we don't see it. For us, it is like server-side-client-discovery and just one API call, but it is not true.

Now, we are getting data by our service names. So, the names could not be changed at all. 

Here is an interesting question: After a Eureka client gets service information by calling Eureka server, does it still call Eureka server for every subsequent call to that service?
Answer is - No! It just calls once for the info and caches it on its internal cache. 

Another thing - The annotation is @LoadBalanced is because that does not only the service-discovery but also client side load-balancing.

How that could happen? 
Let's run another rating-data service jar in another port with - command line. So, that jar will also register itself in our eureka server and we will see two instances running for rating-data-service. Now, if we hit our catalog-service url, we will see nothing is changed. But internally which rating-service is picked by our RestTemplate we don't know. And client-side load balancing is happening. This load-balancing is not total full-proof but it does best what it can do. That is the power and interesting!

Can we know which service are loading internally? Yes! We can.

```java
-- MovieCatalogService

@Autowired
private DiscoveryClient discoveryClient;

```
Now, we got the access of the client and can do some advanced logic based on which service can be loaded in runtime.

How fault tolerance work?

- What if one client goes down? Can our server know that is down? - Yes, service discovery will know. Because eureka clients constantly sending its heartbit to the server. So, for a certain time, if the server does not get the heartbit it will assume that the client service is down.
- What if our eureka server is down? Then how can the client-service will discover other microservice? - The client will know the services from the cache. And it is also manageable by default in Spring. We don't have to do anything special for this. It is the power of @LoadBalanced annotation.


### Part 2

The agenda - 

- Understands challenges with availability
- Making microservices resilient and fault tolerant

What is Fault Tolerance?
Ans - Let's say one service is down, so how much tolerance our full application has to handle that situation? The whole application is going down or part of application is going down. 

What is Resilience?
Ans - How many faults a system can handle.

There is slight difference between fault tolerance and resilience. Sometimes, we can say that a system is very much fault tolerant but not full resilient.

From movie-info service we are making a http call to an external api themoviedb to fetch movie information.

But right now our application is okay with zero fault tolerance. If anything goes wrong, our whole application will go down.

So, how do we make this resilient?

- Run multiple instances
  
What if a microservice instance is slow?

- Consuming threads and tomcat's thread pool is filled up. Here is the link - https://youtu.be/76MEPTM2ARI to understand it better. But what is the solution? Timeout! We can setup timeout in our RestTemplate so that after that time, our client service got an error and returned from there. 
But someone can say that - increasing hardware is a solution. Or increasing the number of threads in tomcat. But no! That is a temporary solution. Because when an application is slow, people refresh it more and more! That means creating more threads again. So, our application will be slower and slower.
  
If we want to configure our RestTemplate with a timeout, then we need write the code like below - 

```java
@Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory();
        requestFactory.setConnectTimeout(3000);
        return new RestTemplate(requestFactory);
    }
```

So, setting this timeout does solve this problem? No! Let's say our request is coming 1000/s in the slow service. Then we are back to the same problem of consuming all the threads from thread pool. Yes it is true that we are the threads will be cleared after timeout but within the timeout our resources will be out totally. So, we can say timeout `partly` solves our issue.

A better and industry standard solution is to use `Circuit Breaker` pattern. It is like electrical circuit breaker which is down automatically when something is wrong in the flow. Here is the details - https://youtu.be/mJ8JSach2P4.

But what is important for circuit breaker is the parameter, how we want to configure it to break the circuit. Here is the link - https://youtu.be/kr-2li6kr9s. 

Let's say our circuit is broken, now we need a fallback to generate a response. What should be the fallback?

- We can throw an error (last resort)
- Give a default fallback response (better approach)
- Save the previous responses in cache and use that when possible. (so, response could be outdated or updated already)

So, let's summary the circuit breaker idea. Why do we need this?

- Failing fast
- Fallback functionality
- Automatic recovery

In a nutshell, `Circuit Breaker Pattern` is made of 3 things

- When to break circuit
- What to do when circuit breaks
- When to resume requests

Here we will use Hystrix as Circuit Breaker in our application. 

- It is a open source library created by Netflix
- Implements circuit breaker pattern so we don't have to 
- Give it the configuration parameters and it does the work
- Works well with Spring Boot

Adding Hystrix to a spring boot microservice

- Add spring-cloud-starter-netflix-hystrix dependency
- Add @EnableCircuitBreaker/ @EnableHystrix
 to the application class
- Add @HytrixCommand to methods that need circuit breakers
- Configure Hystrix behaviour

Note: Hystrix is dperecated now. So, people use Resilience4j now-a-days.
I had to use 
```groovy
ext {
	set('springCloudVersion', "2021.0.6")
}
```
to work with @EnableHystrix annotation with Hystrix.

How does Hystrix work?
It works with dynamic proxy that we already know about AOP - the underlying beauty of Spring framework. The proxy class is constantly checking the output and gives the if-else logic. If we navigate the console sometimes, we will see that ProxyCGLIB is used here.

If our fallback methods are in a same class it won't work, because Spring cannot create proxy object for that, so circuit breaker won't work. But if that is in another class, then Hystrix can take the responsibility of creating proxy.

Here is a nice explanation why Hystrix does not work sometimes? - https://youtu.be/1EIb-4ipWFk

So here we refactor code in another service classes - https://youtu.be/c79blxc0oR0
Now we have granular control over the application.

We can configure Hystrix property with commandProperties field in @HystrixCommand.

We can get HystrixDashboard with `..hystrix-dashboard` dependency. It is a userful UI to get the idea of circuit breaking.

##### Bulkhead Pattern

Its like ship building. Here is the details - https://youtu.be/Kh3HxWk8YF4


### Part 3: Configuration

Example configuration - 

- database credentials
- credentials
- feature flags - flags we use to use different config in our project
- buisiness logic configuration parameters
- scenario testing (A/B testing)
- spring boot configuration

Different types of config files -

- XML files
- properties
- yaml
- json

Goals of configuration
- externalised
- environment specific
- consistent - let's say we have 10 instances of a single project, all should have same configurations
- version hisory - we want to track all the configuration changes
- real-time management 

How can we read property from properties file

- @Value("${property-key}") 

We can also reference from one property to another

```properties
my.greeting=hello
my.description=Welcome home ${{my.greeting}
```

To override the internal properties file, we can set another external application.properties in the same folder the jar is, the jar is gonna load with the external properties file when we start jar using `java -jar {}.jar`. 

Another way is to directly pass the key-value pair in command line arguments, like

`java -jar {}.jar --my.greeting=greeting from command line argument`

That means, my.greeting is now overrides internal my.greeting.

Let's say somehow, our properties file misses a key which is used in application. Then our application won't start. We can provide a default value for that key in our code, so that application gets a default value.
`@Value("${my.greeting: default value}")`

We can also get list of values from properties file
```properties
my.list.values=One, Two, Three
```

```java
@Value("${my.list.value}")
private List<String> values;
```

We can also get Key-value pair
```properties
dbValue={connectionString: 'http://...', username:'foo', password:'bar'}
```

```java
@Value("#{${dbValues}}")
private Map<String, String> values;
```

#### Using @ConfigurationProperties

It is useful when we have group of configurations. Let's say we have below config.

```properties
db.connection={connectionString: 'http://...', username:'foo', password:'bar'}
db.host=127.0.0.1
db.port=8080

```

Now, we will create a bean to get all the informations like below - 

```java
@Configuration // making a bean
@ConfigurationProperties("db") // db is the prefix to pull from properties
class DBSettings {

	private String connection;
	private String host;
	private int port;

	// public getter, setter
}

// using from other class.

@Autowired
public DbSettings dbSettings;
```
That is how we can use bulk properties.

So, till now we have seen two ways to get values. When to use what?

1. @Value is to be used when we need to use a configuration in a single place
2. @ConfigurationProperties is to be used when we need bulk config in different places. Then we connect the bean with a single line. A perfect example is db-connection. 

#### Actuator

We can add the actuator dependency, then add below line in properties file - 

`management.endpoints.web.exposure.include=*` makes all the endpoints created by actuator, exposed.
Now, if we type `localhost:8080/actuator/configprops` we will see all the configurations created by spring and by us.

But the problem with properties file is long configurations name. So, there is another approach - using `yaml` files.
yaml was used to stand for - Yet another markup language, then the meaning is changed - YAML Ain't Markup Language.

In yaml format, we can nested our configs and use in single key like properties - both. Below is the example - 

```yaml
app:
	name: My app
	description: welcome to ${app.name}

my:
	greeting: Hello world
	list:
		 values: One, Two, Three

db:
	connection: "{connectionString: 'http://...', username:'foo', password:'bar'}"
	host: 127.0.0.1
	port: 1200
management.endpoints.web.exposure.include="*"
```
See, we don't need to indent the last config. We keep it as usual. And by default yaml sets it with string or int. So, if we need to setup another format, we need to cover in "" where we used in `*` or connection - `{}`.


### Spring Profiles

Normally if no profile is set, then `default profile` is set. But we can add multiple profiles in the project with the convention `application-${profile-name}.extn`.

And we can set a specific profile in our base (default profile) application.properties file like below-
`spring.profiles.active=test`
Now, our application will load `application-test.properties` file at runtime.

But isn't that odd that we specify a certain profile to look at, in our default profile?

Actually the fact is our default profile (application.properties/yml) is always active. Whatever the other profile we set, always sits on top of the default profile. That means, if we set a specific config in our specific profile and default profile both, then specific profile is gonna win, otherwise all the default profile's configurations will be applied. Actually configs are merged from default profile to other profiles. And, there could be multiple profiles active. 

But still we need to set the default profile to look at in our source code aka our jar file. So, how can we dynamically apply the profile without modifying the jar? 

`java -jar ${...jar} --spring.profiles.active=test` 

- just using the basic overriding strategy of config from properties.

We can also instantiate our beans based on profiles, like - 

```java
@Repository
@Profile("dev")/ @Profile("production")
public class LocalStudentRepository {

}
```
This feature is super powerful but we need to be careful using it. Because creating beans dependent on profile can make huge mess and clutter our business logic.
When we don't specify @Profile annotation, actually it is set to default profile, like - `@Profile("default")`, as default profile is always active.

We can also use Environment object in Spring Boot like below - 

```java
@Autowired
Environment environment;

// now we can get profiles, properties from the environment object.

```
So, with `environment` object we can 

1. can look up profiles - should never do it, as affects testability
2. can look up properties - also should never do it, always fetch value with @Value and ${}.

#### Config as a microservice

Some open source projects for these.

- Apache Zookeeper
- ETCD - distributed key-value store
- Hashicorp Consul
- Spring Cloud Configuration Server
  
The magic of spring cloud server is, the other services will push their properties aka configurations into their own Git repo and our config server will read this and expose to that service at runtime without any re-deployment.

But, what is the url pattern?
{base-url}/{file-name}/{profile}

http://localhost:8888/application/default

Here we write the config repository like below - 
`spring.cloud.config.server.git.uri=/Volumes/Macintosh HD - Data/Study/Projects/My Projects/configrepo`
configrepo is just a basic folder containing application.properties file. But it is git initialized and committed. If we don't commit, our config server can not read the values.

That is the beauty of config server.

But now we will connect our other services with this config server. That means, our microservices will read their configurations from the config-server, not on their own.

For that, we need to add the below dependency in our client service - 
`implementation 'org.springframework.cloud:spring-cloud-starter-config`.

Then in the properties file of this service, we need to add a configuration of the config server url like below - 
`spring.config.import=optional:configserver:http://localhost:8888
`. Now, if we reload the client server and try to fetch a key, it will fetch it from the config server.

Note: If you change anything on the configrepo folder, make sure you `commit` it, otherwise changes won't be reflected.

Let's say we want to make a service specific properties file (like setting a port) in our configrepo folder and we want our service reads that properties file at runtime. For that, we need to create a file of that service name like below 
`{service-name}.properties`, here service-name is defined in service's application.properties file like below 
`spring.application.name = `. 

After creating the specific properties file, we can access it with `{host}/{service-name}`, like `http://localhost:8888/rating-data-service/default`. Now, our service can access the configs from this specific file.

When we commit in our configrepo folder, our config server automatically gets the updated value from the files. But the problem is, our client application still holds the previous value got at startup, not refreshed automatically at runtime.

To make that, we need to add actuator dependency 
`	implementation 'org.springframework.boot:spring-boot-starter-actuator'`, then add @RefreshScope at the controller. The RefreshScope gets the updated dependency injected value every-time url is hit.

To get the updated value now we need to call the api `localhost:8083/actuator/refresh` POST method with no body. (not sure) our values should be updated from the client application without restarting this application server.
*Above dynamic refreshing strategy did not work in mine*

Note: We can use ${HOME} or, ${JAVA_HOME} thigns in properties file and they will be finalised at runtime.
#### Configuration Best Practices

Here is the nice video with best practices - https://youtu.be/AiGCx0raQfs

Here is a nice blog for applying best practices on microservices - https://12factor.net/