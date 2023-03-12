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