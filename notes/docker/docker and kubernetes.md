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