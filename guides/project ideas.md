
### Idea 1

Build an HTTP web server from scratch.

The server should serve static files, and as an extension, should allow you to define and serve APIs, like Flask. Use your favourite programming language, but build one from absolute scratch. It is a week-long project, but if you do it well, you will learn a ton of things, including,

1. what protocols really are and the HTTP protocol
2. writing a protocol parser - HTTP request lines and headers
3. creating TCP sockets, binding to ports, and listening for connections
4. reading and writing large data over sockets
5. how servers handle headers and the actions they take
6. handling multiple connections using threads and event loops
7. hadling different paths to serve static files
8. how to write route handlers to serve APIs