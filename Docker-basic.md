### 1. How is Docker different from standard virtualization using VMs?

Virtual Machines (VMs) virtualize the underlying hardware. They run on physical hardware via an intermediation layer known as a hypervisor. They require additional resources are required to scale-up VMs.

They are more suitable for monolithic applications. Whereas, Docker is operating system level virtualization. Docker containers userspaceace on top the of host kernel, making them lightweight and fast. Up-scaling is simpler, just need to create another container from an image.

Generally, Docker is more suitable for Microservices based cloud applications.

### 2. What are the major components of Docker Architecture?
The four major components of Docker are daemon, Client, Host, and Registry

`Docker daemon:` It is also referred to as ‘dockerd’ and it accepts Docker API requests and manages Docker objects such as images, containers, networks, and volumes. It can also communicate with other daemons to manage Docker services.
`Docker Client:` It is the predominant way that enables Docker users to interact with Docker. It sends the docker commands to docked, which actually executes them using Docker API. The Docker client can communicate with more than one daemon.
`Docker Registry:` It hosts the Docker images and is used to pull and push the docker images from the configured registry. Docker Hub is the public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. However, it is always recommended for organizations to use own private registry.
`Docker Host:` It is the physical host (VM) on which Docker Daemon is running and docker images and containers are created.

### 3. What is a Volume in docker?

A data volume is a specially-designated directory that is located outside of the root filesystem of a container (i.e. created on the host), designed to persist data, independent of the container’s life cycle. This allows sharing data within containers by importing volume directory in other containers.

Data volumes provide several useful features:

* Data volumes persist even if the container itself is deleted.
* Data volumes can be shared and reused among containers.
* Changes to a data volume can be made directly.
* Volumes can be initialized when a container is created.

### 4. When is .dockerignore file used?

A typical Dockerfile contains one or more COPY commands to copy files and/or folders from the developer machine to the docker image, which eventually become part of the container. While copying folders to a docker image, it is quite possible that some unwanted files are also copied to the image. This may create a bulky image and hence cause performance issues in the container.

In order to avoid this, we can create a file named .dockerignore along with Dockerfile in the same directory. This file is used to list all the files and directories that need to be excluded while copying folders onto the image. It contains a pattern and none of the files matching it is added to the image. This helps to avoid unnecessarily sending large or sensitive files and directories to the daemon and potentially adding them to images.

### 5. What is docker-compose?

Compose is a tool provided by Docker for defining and running multi-container applications together in an isolated environment. Either a YAML or JSON file can be used to configure all the required services like Database, Messaging Queue along with the application server. Then, with a single command, we can create and start all the services from the configuration file.

It comes handy to reproduce the entire application along with its services in various environments like development, testing, staging and most importantly in CI as well.

Typically the configuration file is named as `docker-compose.yml`. Below is a sample file:
```sh
version: '3'
services:
  app:
    image: appName:latest
    build: .
    ports:          
    - "8080"   
    depends_on:
      - oracledb
    restart: on-failure:10    
  oracledb:
    image: db:latest 
    volumes:
      - /opt/oracle/oradata
    ports:       
      - "1521"
```
### 6. What is Docker Hub?

Docker Hub is a service provided by Docker for finding and sharing container images. The default version of Hub is the cloud-based registry that hosts all the public docker images like Ubuntu, Linux, etc.

We need to create repositories to push and pull the docker images, allowing us to share container images within our team, organization, customers. In the case of public repositories, we can share the images with the entire Docker community.

Docker images are pushed to Docker Hub through the ‘docker push’ command. A single Docker Hub repository can hold many Docker images.

It also allows you to link repositories with GitHub in order to automate building, testing and deploying of our application images. It provides a centralized resource for container image discovery, distribution and change management, collaboration and workflow automation throughout the development pipeline.

We can also use third-party Repository tools like Nexus and JFrog Artifactory to store and manage docker images.

### 7. Explain basic Docker usage workflow.

Everything starts with the Dockerfile. The Dockerfile is the source code of the Image.
Once the Dockerfile is created, you build it to create the image of the container. The image is just the "compiled version" of the "source code" which is the Dockerfile.

Once you have the image of the container, you should redistribute it using the registry. The registry is like a git repository -- you can push and pull images.

Next, you can use the image to run containers. A running container is very similar, in many aspects, to a virtual machine (but without the hypervisor).

### 8. What are the steps involved while using Docker for application development?

All the steps below are based on the prerequisite that Docker is already installed on the machine:

* Create Dockerfile:
The initial step is to create a Dockerfile file using a suitable base image along with all the required steps/commands, like setting environment variables, adding application jar, etc. This creates several layers on the existing base image.

* Build image:
Once the Dockerfile is ready, we can either use docker command or via a Gradle task to generate a docker image. This image contains all the application dependencies required to run the application in a container.

`docker build -t test/security tool`.

* Run the image:
Once the docker image is built, we can create and start the container using command.

`docker run --name rest_tool test/security tool`.

* Start Containers using Compose:
In case we have multiple containers constituting an application like database, messaging queue, etc.; then it is advisable to use docker-compose to run multiple containers simultaneously. It is also useful in the CI pipeline for running the application and performing tests.

`docker-compose up`

* Test the Application:
After the containers are up and running, the application is ready for Integration or Acceptance tests to be performed. Ideally, it is integrated into the CI pipeline for determining if the new code changes are affecting the existing flows.

* Push image:
Typically in a container-based development environment, the deliverable artifact is a docker image. This image needs to be published to an internal Registry like Artifactory so that it can be propagated to next levels like Continuous Delivery and Deployment pipelines.

`docker push test/securityTool`

* Production orchestration:
Ideally, organizations need to use orchestration tools like Kubernetes to run the containers in a Pod to perform load balancing, service discovery, etc in a production environment. It also helps in providing scalability and high availability of the application.