Here is the playlist of this notes https://www.youtube.com/@Java.Brains/playlists

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
