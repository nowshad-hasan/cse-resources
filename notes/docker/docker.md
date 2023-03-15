## Course followed - [Docker and Kubernetes: The Complete Guide By Stephen Grider](https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/)

### Why use docker?

Let's say we are trying to install a software in our PC. But it creates problem sometimes, right? Maybe some broken link or version mismatch etc. So, Docker is trying to solve this problem, so that we don't have to worry about the installation anymore. So, in a nutshell docker makes our life lot more easier installing software without dependencies.

Here are two things two understand clearly.
![Docker Universe](../../images/docker-universe.png)

Using docker we actually get images, which contain all the necessary configurations, properties etc. And we just run the image in a docker, aka - container. And it runs!
![Docker container to image](../../images/docker-image-container.png)

Docker is made of two things - 

- Docker client (Docker CLI): It is the tool we will give the command to interact with. It is basically takes our commands.
- Docker server (Docker Daemon): This is under-the-hood that is responsible to create images, running containers etc.

Let's write our first docker command `docker run hello-world`. Then we will see bunch of text there. Most importantly, this `Unable to find image 'hello-world:latest' locally`. 
Actually what the command does, Docker Client takes the coomand, then goes to Docker Server for this image. Docker server searches in its own `Image Cache` for this image. But not found yet. Then, it goes to Docker Hub where all the popular images are already given. It goes there, download it and save in its local 'Image Cache' and gives us the output. Next time, we enter the same command, it won't show the line *Unable to find image 'hello-world:latest' locally*.

Here is a nice overview [here](https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/learn/lecture/11436632?start=0#overview).

Container basically uses `namespacing`, `control groups` for process separation and OS resource sharing. But the problem is this separation is Linux specific, not in Windows/ Mac. So, how we install docker in mac or windows and it works perfectly? 
![Separation](../../images/namespacing.png)
Actually when we install docker in mac/linux, it runs on linux subsystem, which contains linux kernel underneath. That's how docker can work there.
![stack](../../images/stack.png) 

We can verify running the command `docker version` and see the OS/Arch section, written `linux/amd64`.

#### Overriding docker run

Actually what an image does, it creates a container. And it has a starting section, which is triggered by `docker run`. But we can override it by passing our own command like that `docker run {image} {custom command`.
Like - `docker run busybox ls` / `docker run busybox echo hi there`
If we run these, the container has this ls/echo command installed. That's why it will work. But if we type 
`docker run busybox cd bin` - it will say that `cd` is not found. Same thing is gonna happened if we type 
`docker run hello-world ls` - in the hello-world container, ls is not installed, so it will show that it is not recognizing this command.

#### Listing running containers

What we have seen so far, are the started and immediately stopped with echo something or printing list of things. But sometime, we want our process to run for further.
To list all the currently running container on our machine, type `docker ps`. If we want to see an example, type `docker run busybox ping google.com` in one window. In another window, type *docker ps*. It will show Container Id, image and other details. Here, ID is the important one. We will need it for future reference. If we need to list all the running containers ever on our machine, type 
`docker ps --all`.

#### Docker lifecycle

Actually *docker run* is a combination of two commands,
docker run = docker create + docker start -a {container-id}

docker create creates a container from an image. It gives the container id as output when we run `docker create`. Then `docker start {id}` - runs the container and just exits after running without showing us any output like `docker run`. So, if we want to get a nice output, we need to type, `docker start -a {id}`.
```bash
docker create hello-world
docker start -a {id}
```
`-a` is for attaching. Here is a nice diagram what actually happens under the hood.
![Docker lifecycle](../../images/create-start.png)

We can re-start a container with an ID. Where do we find it? From `docker ps --all`. Same as like before - 
`docker start -a {id}`. But here is an interesting concept. We can't override the default command by `docker start` when restarting. It will execute exact same command (or we can call it re-issuing), when it was first run (by docker run or docker start). We cannot pass something like that - `docker start -a {id} echo hi there`. But what we can - 

```bash
docker run busybox echo hi there
docker ps --all -> get the id
docker start -a {id} -> get the same output from docker run
```
Here if we try something like - `docker start -a {id} echo hello`, it will say that 'you cannot start and attach multiple containers at once'.
Here is nice explanation video - https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/learn/lecture/11436652#overview
![Restarting a docker](../../images/restart.png)

But let's say we are done and want to remove all the stopped containers and images from `docker local cache`. 
So, we type `docker system prune`.

#### Collect logs from container

We can get the logs emitted from a container after it is stopped and exited. We saw the output with `docker run`, or `docker start -a {id}`. But there is another way - 
`docker logs {id}`. 

```bash
docker create busybox echo hello there
docker start {id}
docker logs {id}
```

#### Stoping containers

We can stop/kill a container by `docker stop {id}` or `docker kill {id}` command. But what is the difference? 
`docker stop` sends `SIGTERM` signal to the container where `docker kill` sends `SIGKILL` signal. As we already know *SIGTERM* command does not always kill a process but *SIGKILL* always kills a process. But in terms of docker, when we type `docker stop {id}`, it tries to stop and kill the process, if not killed within 10 seconds, it will kill it, that means it will send the SIGKILL signal to the process. Let's see an example. If we type 
```bash
docker create busybox ping google.com
docker start {id} 
docker ps
```
We will see that a docker process is running. `ping` is basically a infinite running process and it does not understand SIGTERM command. So, if we try `docker stop {id}`, it will try to stop within 10 seconds, then it will send SIGKILL signal to kill the process. But if we try `docker kill {id}`, it will immediately kill the process.

#### Execute additional command inside a docker

Let's say, we installed redis inside an image. Now, as usual we want to execute `redis-cli` to store and get value. But how to do this? 
So, let's type - `docker run redis`, now it will create and start the container in that terminal window. But, let's open another window, and type `redis-cli`, no - it won't work. Because, typing redis-cli is trying to find the command in our computer not in that container. So, to execute that command we need to type 
`docker exec -it {id} {command}` -> command = redis-cli or else
exec = execute, it = allow us to provide input into container.
We need to find the container id typing `docker ps`. If we type that above command without `-it` flag, it will start and immediately stop as it is not allowing us to provide input into container.

But the power of `-it` flag is more. Every process has three sections - 

- STDIN - standard input
- STDOUT - standard output
- STDERR - standard error
  
Here is a nice diagram to explain these things.
![stdin stdout](../../images/stdin.png)
We can type that `-it` flag separately - `-i -t`. Here i = STDIN, t = nicely formatted input to take. To test, we can type without `-t` command like - `docker exec -i {id} redis-cli`.

#### How to open a shell terminal inside a container

Let's say we want to open a terminal inside a running container. We type - `docker exec -it {id} sh`. `sh` for *shell*. So, then we are inside a shell from inside a container which is running from a shell. Make sure, the container is running on the other terminal, otherwise entering into the container's shell is impossible. It will also work for `docker run busybox ping google.com`, though if we run custom command in there like, `docker run busybox cd bin/` - it will not recognize cd. But if we start it with ping, then call it from other terminal `docker exec -it {id} sh` and entering into shell, we can type `cd` without error. That's the true beauty.

**Note:** How to exit that double shell? `CTRL+D` or `exit` is on the rescue.
There is an alternative for this. We need not to open a extra process for running and start shell in other terminal. We can directly open a shell at container startup with - `docker run -it busybox sh`. The only downside of this approach, we cannot open another process for this like before where we keep running a separate process with `ping` and use terminal from another window. So, both approaches are available, we just follow what we need. 

**Note**: Creating two containers from a single image are two separate VM, so those will not share any memory or RAM or anything. Creating a file inside one container can not be accessible from other container. We can check this with `docker run -it busybox sh` twice, then creating a file inside one, and trying to see from other container. It is easy to understand.

#### Creating a docker file

Here is basic flow of creating a *Dockerfile* (not Dockerfile.js or something)- 

- Specify a base image - use an existing docker image as a base
- Run some commands to install additional programs - download and install a dependency
- Specify a command to run on container startup - tell the image what to do when it starts as a container

Let's create a file named `Dockerfile` with below lines to create an image with redis-server - 

```bash
FROM alpine
Run apk add --update redis
CMD ["redis-server"]
```
Now, give the command `docker build .` in that file's folder. In the output of this command we will see that output `sha:{image-id}`. As usual, just run `docker run {id}`.

Here is a visual of the command.
![Instruction](../../images/instruction.png)

Let's tear down all these.