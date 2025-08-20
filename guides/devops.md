## Contents

- [Contents](#contents)
  - [Article](#article)
  - [CI/CD](#cicd)
  - [Learning materials](#learning-materials)
  - [Docker](#docker)
  - [Kubernetes](#kubernetes)
  - [Github Actions](#github-actions)
  - [Courses](#courses)
  - [Blogs](#blogs)
  - [Videos](#videos)
  - [ELK Stack](#elk-stack)
  - [GitHub Repo](#github-repo)
  - [Collection](#collection)
  - [Interview questions](#interview-questions)
  - [Project based implementation](#project-based-implementation)

### Article

* What Is a File System? Types of Computer File Systems and How they Work – Explained with Examples [Freecodecamp](https://www.freecodecamp.org/news/file-systems-architecture-explained/)
* Make Jenkins logs pretty [Opensource.com](https://opensource.com/article/21/5/jenkins-logs)
* Automating Task with Cron and Anacron [Sihamsharif](https://sihamsharif.me/linux/automating-task-with-cron-and-anacron/)
* Containers Patterns [Link](https://l0rd.github.io/containerspatterns/#1)
* Kubernetes Production Patterns [Github](https://github.com/gravitational/workshop/blob/master/k8sprod.md)
* Cloud-Native – Why Software Developers Should Look Into It [Link](https://blog.adrianstanek.dev/p/cloud-native-why-devs-should-know-it)

### CI/CD
* Learn How to Set Up a CI/CD Pipeline From Scratch [DZone](https://dzone.com/articles/learn-how-to-setup-a-cicd-pipeline-from-scratch)
* GitHub Actions Certification – Full Course to PASS the Exam [Freecodecamp-Youtube](https://youtu.be/Tz7FsunBbfQ?si=qEVctgtGkz3GPUQb)

### Learning materials

- [DevOps-The-Hard-Way-AWS](https://github.com/AdminTurnedDevOps/DevOps-The-Hard-Way-AWS) by [Michael Levan](https://michaellevan.net/)

### Docker
* The Docker Handbook – Learn Docker for Beginners [Freecodecamp](https://www.freecodecamp.org/news/the-docker-handbook/)

### Kubernetes
* The Kubernetes Handbook – Learn Kubernetes for Beginners [Freecodecamp](https://www.freecodecamp.org/news/the-kubernetes-handbook)
* Ultimate Certified Kubernetes Administrator (CKA) Exam Preparation Guide [Github](https://github.com/techiescamp/cka-certification-guide?tab=readme-ov-file#manage-the-lifecycle-of-kubernetes-clusters)

### Github Actions

- GitHub Actions Tutorial | From Zero to Hero in 90 minutes (Environments, Secrets, Runners, etc) [Youtube](https://youtu.be/TLB5MY9BBa4?si=rq4Lnd0QzP2KMo3N)

### Courses

- Linux Mastery: Master the Linux Command Line in 11.5 Hours [Udemy](https://www.udemy.com/course/linux-mastery/)
- HashiCorp Certified: Terraform Associate 2025 [Udemy](https://www.udemy.com/course/terraform-beginner-to-advanced/)
- Terraform on AWS with SRE & IaC DevOps | Real-World 20 Demos [Udemy](https://www.udemy.com/course/terraform-on-aws-with-sre-iac-devops-real-world-demos/)
- Certified Kubernetes Administrator (CKA) with Practice Tests [Udemy](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/)

### Blogs

- [Devopscube](https://devopscube.com/)

### Videos 

- Mastering curl with Daniel Stenberg (lead developer) [Youtube](https://youtu.be/V5vZWHP-RqU?si=_K2_WebZYUwoAdtR)
- Basic cURL Tutorial[Traversy Media](https://youtu.be/7XUibDYw4mc?si=TEva0rPXem7xdoub)
- Introduction to Linux – Full Course for Beginners [Freecodecamp](https://youtu.be/sWbUDq4S6Y8?si=V75IwMieS_tc9VNr)
- Docker Networking Crash Course [Hussein Nasser-Youtube](https://youtu.be/OU6xOM0SE4o?si=kCSM6lnNo13KfanX)
- Linux File System/Structure Explained! [Youtube](https://youtu.be/HbgzrKJvDRw?si=F0rnjeQKtNA52AhG)
- DevOps Prerequisites Course - Getting started with DevOps [Freecodecamp-Youtube](https://youtu.be/Wvf0mBNGjXY?si=oa_kl4zRfu3VqjOq)
- Certificates and Certificate Authority Explained [Hussein Nasser](https://youtu.be/x_I6Qc35PuQ?si=msjMrRCP6HHAR3kF)
- AWS Cloud Development Kit (CDK) Crash Course [Freecodecamp-Youtube](https://youtu.be/T-H4nJQyMig?si=djlmX4KujlvMoauj)
- How to Put a Website Online: Template, Coding, Domain, Hosting, and DNS [Freecodecamp-Youtube](https://youtu.be/NQP89ish9t8?si=eYww9MURGj585MH3)
- AWS Certified Solutions Architect - Associate 2020 (PASS THE EXAM!) [Freecodecamp](https://youtu.be/Ia-UEYYR44s?si=E9GF2j-BDSL9PXal)
- cURL Verbose Mode Explained (and how I use it to Troubleshoot my Backend) [Hussein Nasser-Youtube](https://youtu.be/PVm0YEEuS8s?si=HICt_EKWYNVE1ACw)
- DevOps Engineering Course for Beginners [Freecodecamp](https://youtu.be/j5Zsa_eOXeY?si=Rtn2PP2KqdOjkxfs)

### ELK Stack
- Elasticsearch Course for Beginners [Freecodecamp-Youtube](https://youtu.be/a4HBKEda_F8?si=XvNCMFCbYRq7tI_J)

### GitHub Repo
- [DevOps Roadmap](https://github.com/milanm/DevOps-Roadmap)

### Collection

- Devops exercises [Github](https://github.com/bregman-arie/devops-exercises)

### Interview questions

Docker
 Scenario-Based Interview Questions 

1. You are running a containerized application that crashes intermittently without logging anything useful. How do you debug this behavior?
2. Your CI/CD pipeline pushes a new Docker image that fails only in production, not in staging. How would you isolate and resolve the discrepancy?
3. Your Docker image builds are inconsistent across developers’ machines. How would you ensure repeatable builds?
4. How would you securely inject secrets into a container without hardcoding them in Dockerfile or exposing them in environment variables?
5. A container using a volume is not syncing changes back to the host machine. How do you diagnose and resolve this?
6. You need to migrate your local Docker-based app to Kubernetes. What Docker-specific configurations might cause issues during the migration?
7. Your container uses a large base image and takes a long time to download in remote environments. What strategies can you apply to improve this?
8. You notice a container has exited with an OOMKilled (Out Of Memory) status. How do you investigate and prevent this?
9. How would you monitor file system usage and inode exhaustion in a running container?
10. Your team needs to run GPU-based containers on a shared host. How do you design a secure and performant setup?
11. You want to roll back to a previous container version but don't have the previous Dockerfile. How do you retrieve and use the old image?
12. You need to isolate a set of containers with custom firewall rules. How do you implement this using Docker’s networking capabilities?
13. A container exposes multiple ports, but some are not accessible externally. How do you verify and expose the correct ports?
14. How do you configure Docker for a multi-architecture build (e.g., building for x86 and ARM simultaneously)?
15. Your Dockerfile uses ADD to fetch remote URLs, but the builds fail due to SSL errors in CI. How do you debug and solve this?
16. You notice layers in your Docker image are not being cached during builds. What could be causing this?
17. You are required to enforce immutability for Docker containers in production. How would you approach this?
18. How would you implement a security scanning workflow integrated with your CI/CD process for Docker containers?
19. What would you do if Docker container logs are rotated too frequently and important logs are being lost?
20. You suspect your container image has been tampered with. How do you validate its authenticity?
21. How do you enforce policy controls such as image whitelisting in a Docker deployment?
22. A base image you use has been deprecated. How do you manage and migrate all dependent services with minimal downtime?
  
### Project based implementation

Step-by-Step Implementation:

1. Local Development Setup
 - Create a Flask application
 - Implement Celery with Redis for background tasks
 - Configure Gunicorn as the WSGI server
 - Set up Nginx to serve static files
 - Create Docker containers for each service

2. Containerization
 - Write a docker-compose.yml file
 - Define services: Flask app, Redis, Nginx
 - Ensure containers can communicate seamlessly
 - Test local deployment and service interactions

3. Cloud Infrastructure Preparation
 - Set up AWS account (free tier available)
 - Create VPC with public and private subnets
 - Provision RDS PostgreSQL instance
 - Prepare for ECS deployment

4. Terraform Infrastructure
 - Write Terraform modules for:
 * VPC configuration
 * Subnets (public and private)
 * Application Load Balancer (ALB)
 * ECS cluster and services
 * Security groups
 * Route53 for domain management
 - Implement best practices for infrastructure as code
 - Configure SSL certificates for secure connections

5. Service Architecture
 - Place Nginx behind load balancer
 - Position Flask app with Redis for Celery
 - Use AWS Service Connect for service discovery
 - Ensure proper network segmentation

6. Continuous Deployment
 - Create GitHub Actions workflow
 - Automate build process
 - Implement deployment to AWS ECS
 - Add steps for:
 * Code building
 * Docker image creation
 * Terraform infrastructure updates
 * Application deployment

7. Network and Security
 - Place ALB in public subnet
 - Deploy services in private networks
 - Implement secure communication paths
 - Configure domain routing
 - Set up SSL certificates

Github project - [web-app-on-aws-ecs](https://github.com/akhileshmishrabiz/web-app-on-aws-ecs)
Prod implementation - [web-app-on-aws-ecs](https://github.com/akhileshmishrabiz/web-app-on-aws-ecs/tree/prod-implementation)
