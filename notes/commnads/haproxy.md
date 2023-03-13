#### TCP config
We can create a file `tcp.cfg` like below.
```config
global
 maxconn 4096
defaults
 log global
 mode tcp
 timeout connect	5000
 timeout client		50000
 timeout server		50000

frontend localnodes
 bind *:8888
 default_backend nodes

backend nodes
 server server1 127.0.0.1:1111
 server server2 127.0.0.1:1212
```

#### HTTP config
We can create a file `http.cfg` like below.

```config
global
 maxconn 4096
defaults
 log global
 mode http
 option httplog
 option dontlognull
 retries 3
 option redispatch
 timeout connect	5000
 timeout client		50000
 timeout server		50000

frontend localnodes
 bind *:8888
 acl app1 path_end -i /app1
 acl app2 path_end -i /app2
 use_backend app1servers if app1
 use_backend app2servers if app2
 

backend app1servers
 mode http
 server server1 127.0.0.1:1111 check
 server server2 127.0.0.1:1212 check

backend app2servers
 mode http
 server server3 127.0.0.1:1313 check
 server server4 127.0.0.1:1414 check
```
There 127.0.0.1:1111 => http://localhost:{port}

We can run these config like - `haproxy -f {config}.cfg`
In Ubuntu, a default haproxy.cfg is available under `/etc/haproxy` folder. But we can always create a custom .cfg file, and start HAProxy with above command with the file. 

