## 1. What is Docker architecture?

Docker is having a  client-server architecture. The Docker client talks to the Docker daemon. Docker daemon helps in building, running, and distributing your Docker containers. The Docker client and daemon can co-exist on the same system or on different systems. This client-daemon connection is done via REST API.

* The Docker daemon
The Docker daemon is also known as docker engine manages docker objects such as images, containers, networks, and volumes. Communication with other docker daemons also happens through this. Docker daemon is started by the command “dockerd”.Docker daemon talks with the kernel manages system calls for creation, running, destroying, etc of containers.

* The Docker client
The Docker client is the utility we use when we use commands such as docker run. The Docker client sends these requests to Docker daemon, which does the rest. One Docker client can connect with more than one Docker daemon.

* Docker registries
A Docker registry is where the Docker images are stored. Docker Hub is a public registry and docker searches for images on Docker Hub by default. Other registries are Docker Datacenter (DDC), which also includes the Docker Trusted Registry (DTR). Whenever docker pull or docker run commands are executed, the required images are pulled from the registry. We can also push the newly created docker images to Docker registry.

## 2. What is Docker Swarm?

From Docker Engine version 1.12 Docker swarm mode was introduced that creates a cluster of one or more Docker Engines. A swarm can have more than one node which could be either a  physical or virtual machines that run docker engine.

Main Features are

`Cluster management:` Docker Swarm could be created using Docker CLI  where application services could be deployed.No need of any other clustering tools.
`Distributed design:` At deployment time, the Docker Engine handles any specialization at runtime. Both manager and worker nodes use the Docker Engine.
`Declarative method:` A declarative method is used for defining the desired state of various services. For example, an application with a web front end service could be combined with message queueing services and a database backend.
`Scaling:` Whenever we need to scale the swarm, one of the manager nodes automatically adapts by adding or removing tasks.
`The desired state attained:` The swarm manager node checks for the actual state and your expressed desired state to bring the cluster back to the desired state. For example, if a service needs to run 20 replicas of a container, and each worker machine hosting 10 of them, the manager always creates new ones to replace the replicas that crashed.
`Multi-host networking and service discovery:` The swarm manager assigns Ip addresses to the containers from the pool when it initializes or updates the application. Each service in the swarm has a unique DNS name and load balancer for the containers. Swarm helps the user to define how to distribute load balancing within service containers.

## 3. What is developer workflow of docker?

The workflow starts with:

* Attaining the docker image
If the docker image is present in Docker Hub or that kind of Docker registries the images are selected from them. Developers can also create a great number of images if need be. Images have a layered architecture. Mostly a combination of a base layer and then a customised layer is used to create a docker image. There are several types of files that help to build images and they are kept in respective repositories. Some of the important files to consider to create docker images are docker files, docker-compose.yml files, etc. Multiple containers could be run together using docker-compose.yml files. Some of the types of repositories where the images are stored are

`Version Control` - This is used mainly used to store text-based files such as Dockerfiles, docker-compose.yml, and configuration files. Some of the version control systems are git, svn, Team Foundation Server, VSTS, and Clear Case.
`Repository Manager` - Large binary files such as that of Maven/Java, npm, NuGet, and RubyGems are stored in repository managers.Whereas smaller binary files could be saved to version control. Examples include Nexus, Artifactory, and Archiva.
`Package Repository` - Packaged applications for example like an operating system such as CentOS, Ubuntu, and Windows Server are stored in the package repository. Examples are yum, apt-get, and package management. They can also run several containers together described in a docker-compose.yml file and test the application.

* Deploy on UCP(Docker Universal control plane)
A CI/CD system runs unit tests, builds the applications, and create a  Docker image on the Docker Universal Control Plane (UCP). If the image passes all tests they are signed using Docker Content Trust and shipped to Docker Trusted Registry (DTR). The developer could also do testing on an integration environment on UCP in cases where the developer’s machine does not have access to all the resources to run the entire application.

* Push Images
Once the image is ready newer images created are pushed into Docker Trusted Registry. The developer can use the command

`docker push < image name>`
The developer account in DTR is a must have to do this action

* Commit
Once the application is tested on UCP files used to create the application, its images, and its configuration is committed to version control. The commit triggers could be set up to trigger the CI/CD workflow. The images could now be used to create containers.

## 4. What are Dockerfiles?

Dockerfile is a text file that has instructions to build a Docker image. All commands in dockerfile could also be used from the command line to build images.

docker build command creates an image from the dockerfile and its contexts. Contexts are a set of files at a PATH/URL. URL could be any repository location of any version control system. PATH could also be a location in your local system. The following command shows building docker image from the contexts in the current directory and send the context to the docker daemon

$ docker build .
Sending build context to Docker daemon  6.51 MB
...
Root directory or path is not preferred as context path because the entire contents of your hard drive will be transferred to the Docker daemon.

Docker daemon performs a validation of the dockerfile before running instructions in dockerfile. Docker daemon takes on the instructions one by one creating an updated image each time.

Sample Dockerfile :
```sh
FROM ubuntu:16.04
COPY . /app
RUN make /app
CMD python /app/app.py
```
Each instruction in a dockerfile  creates one read-only layer:
```sh
FROM creates a layer from the ubuntu:16.04 Docker image as the base image.
COPY adds files or contexts from your Docker client’s current directory.
RUN builds your application. Here we use make.
CMD specifies the command to be run inside the container.
```
## 5. What are docker-compose.yml files?

Docker compose is a tool that helps to run multi-container docker applications. Compose uses docker-compose.yml which is a YAML file to configure application’s services. Compose workflow is a three-step process. First is to create a dockerfile to build the image, secondly define the services to be run on a container in the docker-compose.yml file and thirdly docker-compose up command starts up your entire applications. dockerfiles and docker-compose.yml files are similar but the main difference is that docker compose file manages multi-container architecture rather than a single container.
```sh
Sample docker-compose.yml file looks like:

version: "7"
services:
  testservice:
    # replace username/repo:tag with your name and image details
    image: <image name>
    deploy:
      replicas: 10
      resources:
        limits:
          cpus: "0.2"
          memory: 70M
      restart_policy:
        condition: on-failure
    ports:
      - "9080:80"
    networks:
      - webnet
```      
This docker-compose.yml is reflected upon below:

* Pull the image from Docker registry .
* Run 10 instances of the pulled image as a service called testservice, with setting limits on each one as, 20% of a single core of    CPU time and 70 MB of RAM.
* Immediately restart containers on failure.
* Map port 9080 on the host system to test services port 80.
* test services containers share port 80 through a load-balanced network called webnet.

## 6. What is Docker volume mounting?

Data is not persistent inside a container. One of the most used methods is Volume mounting.  Another method for persisting data is to include data in the writable layer but that layer is tightly coupled with the host machine where the container is running, thus the data couldn’t be managed easily. Also writing into the writable layer is complex and needs to use a driver. 

To retain data, the best-preferred way is to use a Docker volume mount where it mounts another directory into your container. This directory is on the host which could be seen within the container. The main advantage of volume mounting is

Easy to manage using Docker CLI commands.
We have support for both Windows and Linux systems
Multiple containers can share the volumes.
Volumes are easier to back up
Volume drivers are present to encrypt volume contents and to store on cloud systems etc
The container can pre-populate the new volumes with its contents
Volumes don't increase the size of the containers.
Following commands could be used to manage volumes:

* docker volume create <name> to create the volume
* docker volume ls to list the volumes available to you
* docker volume inspect <name> to get more details about the volume like path, drivers etc.
* docker volume rm <name> to remove the container.
* docker volume prune to remove unused volumes

## 7. What are the basic built-in security features of Docker?

* Kernel namespaces
Namespaces define the context in which names are defined whether it be variable names or function names. In other words, namespace defines the scope of the names. For eg: namespaces are like surnames. Suppose we have two people with the same name “John “ their surname differentiates them.

Each container in docker creates a set of namespaces specific to the container. Hence is the first and a great method of security between containers.

* Control Groups
Control groups facilitate resource accounting and limiting. Control Groups doesn’t allow a container to exhaust the host system’s CPU, memory, disk I/O, etc. It also doesn’t allow data and processes of container to be accessed by another container.

* Docker daemon attack surface
When a “docker run “ command is performed docker client speaks to docker daemon who manages the images and containers. Docker daemon needs root privileges. Extra precaution must be taken to give access only to trusted users to control docker daemon. A  container could even be started from the root directory on your host and the container can alter your host filesystem without any restriction.

* Linux Kernel Capabilities
Containers could be started with a reduced set of capabilities. This would mean that “root” within a container has fewer privileges than the real “root”. This, in turn, reduces the damage by an intruder with root privileges.

## 8. What is the swarm mode Public Key Infrastructure?
Built-in Public Key Infrastructure System helps to secure the deployment of container orchestration systems. Transport Layer Security (TLS) is used in Public Key Infrastructure to communicate with other nodes in a swarm.

When a swarm is initialized with “docker swarm init” command in a docker host, root Certificate Authority (CA) with a key pair is created. This is for securing nodes that join the particular swarm.

“--external-ca”  flag is used with docker swarm init command to use external root CA.

Manager node generates worker token and manager token. Each token has the digest of the root CA certificate and a randomly generated secret. When a new node joins the docker swarm with the worker token the node uses the digesting part to verify the root CA from the manager node. While the leader node uses the secret to approve the new joining node. Manager node issues a certificate to the joining node with a randomly generated node ID

By default, swarm performs the renewal of the certificate every three months but it can be modified with the command

“docker swarm update --cert-expiry <TIME PERIOD> “

In case if the leader-manager node is down we can rotate the root CA within the swarm so that no nodes trust the certificate signed by old root CA. This can be done by the command “docker swarm ca --rotate”.

This command thus creates a cross signed certificate telling the nodes that still trusted  old CA to start verification against new root CA

## 9. What are the basic instructions used in Dockerfile and how they differ from each other?

* FROM instruction    
Usage :

FROM <image> [AS <name>]
Or

FROM <image>[:<tag>] [AS <name>]
Or

FROM <image>[@<digest>] [AS <name>]
FROM helps to set the base image for the following instructions in the Dockerfile. The base image could be one pulled from the remote public repository or a local image built by the user. FROM can appear multiple times in Dockerfile and each time it appears it clears any stage created from previous instructions. Hence usually an AS <NAME> is added to the FROM instruction to identify the last image created just before the FROM instruction.  

* RUN instruction
Usage:

RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
RUN ["executable", "param1", "param2"] (exec form)
RUN instruction creates a new layer on the current image which is used for the next step in Dockerfile.RUN cache is valid for the next build provided “docker build” command is not run with “--no-cache” flag.

RUN in the executable form doesn’t do shell processing like variable substitution.

* CMD instruction
Usage:

CMD ["executable","param1","param2"] (exec form, this is the preferred form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
CMD command param1 param2 (shell form)
CMD provides defaults for executing container. There could only be one CMD instruction but if there are many, only the last one has the effect. If CMD is providing default arguments for the ENTRYPOINT instruction, then the CMD and ENTRYPOINT instructions should be specified with the JSON array format.  

* ADD instruction
Usage:

ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"] (this form is required for paths containing whitespace)
The ADD instruction copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the image at the path <dest>.

* COPY instruction
Usage:

COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"] (this form is required for paths containing whitespace)
The COPY instruction copies new files or directories from <src> and adds them to the filesystem of the container at the path <dest>.Main difference between COPY and ADD is that ADD supports the source to be a URL or tar file.

* ENTRYPOINT
Usage:

ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)
ENTRYPOINT command param1 param2 (shell form)
An ENTRYPOINT allows you to configure a container that will run as an executable. As a best practice ENTRYPOINT should be defined when using the container as an executable and CMD should be used as a way of defining default arguments for an ENTRYPOINT command.CMD could be overridden while running a container with arguments.

* WORKDIR
Usage:

WORKDIR /path/to/workdir
The WORKDIR instruction sets the working directory for RUN, CMD, ENTRYPOINT, COPY and ADD instructions in the Dockerfile.

## 10. What is Docker context?
Docker builds command builds the images from Dockerfiles and “context”.A build’s context is the set of files located in the specified PATH or URL. The PATH is a directory on your local host filesystem while the URL could be a Git repository location, pre-packaged tarball contexts, and plain text files. COPY instruction could be used to reference a file in the context.

In the case of Git repositories, first the repository is pulled into a temporary directory on your local host then after it’s successful, the directory is sent to the Docker daemon as the context.

For eg: docker build https://github.com/dockersample/test.git#container:docker uses a directory called docker in the branch container as the context.

In the tarball context. For eg: docker build http://server/context.tar.gz   

sends the URL itself to docker daemon.Tar file is downloaded on the host the Docker daemon is running on, which need not be the same host from which the build command is being issued.

In text files context a single Dockerfile could be passed in the URL or pipe the file in via STDIN. For eg: To pipe a Dockerfile from STDIN we use the instruction: docker build - < Dockerfile

## 11. How to work with docker build cache?

While building a docker image Docker steps through the instructions in the Dockerfile adding layers to the base image. Every time Docker encounters a new instruction it checks for an existing image in its cache which it can reuse locally. Thus helping to build the final docker image quickly. If you don't want to use cache “ --no-cache=true” option with docker build command is used. Basic rules about cache are as follows:

* After the parent image is in the cache, the next instruction is compared against all child images derived from that parent image to see if anyone of them was built using the next instruction. If not, the Docker builds the image at this step once again and the cache is invalidated.
* For the ADD and COPY instructions, the contents of the file(s) in the image are examined and a checksum is calculated for each file. Last-modified and last-accessed times of the file(s) are not considered in these checksums. These checksums are compared with checksums of the existing images. If there are changes cache is invalidated.
* Unlike the ADD and COPY commands, in the case of a RUN  command, the files updated in the container are not examined to determine if a cache hit exists. In that case, just the command string itself is used to find a match.
* Once the cache is invalidated, all the following Dockerfile commands generate new images.

## 12. What are the networks used in Docker Swarm?

When a Docker Swarm is created or a docker host joins the swarm two networks are created.

* an overlay network called ingress, which manages control and data traffic related to swarm services. This is the default network unless the user specifies other user-defined overlay networks. User-defined overlay networks could be created by using the command “docker network create”ingress network facilitates load balancing among a service’s nodes.
* To encrypt the application data traffic on a given overlay network, use the --opt encrypted flag on docker network create.
* To attach a service to an existing overlay network, use the --network flag to docker service create, or the --network-add flag to docker service update command. Service containers connected to an overlay network can communicate with each other through it.
* bridge network called docker_gwbridge, which connects the Docker daemon to the other docker daemons in a swarm. docker_gwbridge connects the overlay networks (including the ingress network) to an individual Docker daemon’s physical network. It is a virtual bridge that exists in the kernel of the docker host. For customising the bridge network we have to do that before docker host joins the swarm or by temporarily removing the host from the swarm.

## 13. Explain different types of volume mounts in docker.

There are three mount types available in Docker   

* `Volume mounts` are the best way to persist data in Docker. Data are stored in a part of the host filesystem which is managed by Docker containers. (/var/lib/docker/volumes/ on Linux).Main advantages of using volume mounts are
-> Easy to use and backup
-> Docker CLI commands or the Docker API can be used to manage volume mounts.
-> Linux and Windows containers support volume mounts.
-> Volumes could be shared securely among multiple containers.
-> New volumes can use pre-populated content by a container.

 Starting with Docker 17.06, -v or --volume flag and --mount flag could be used for docker swarm services and standalone containers. 
To create a docker volume. For eg:

“docker volume create my-vol” creates new volume  “my-vol”.

We can inspect a volume with the command “docker volume inspect”

For eg:
```sh
“docker volume inspect my-vol “ gives the output

[
{
    "Driver": "local",
    "Labels": {},
    "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
    "Name": "my-vol",
    "Options": {},
    "Scope": "local"
}
]
If we need to start a container with “my-vol”

With -v flag
“docker run -d  --name devtest -v my-vol:/app nginx:latest”. Here nginx images with the latest tag are executed with using volume mount “my-vol”

With --mount flag
“docker run -d --name devtest --mount \ source=my-vol,target=/app nginx:latest”
```
* `Bind mounts` may be stored anywhere on the host system. A file or directory on the host machine is mounted into a container unlike volume mounts where a new directory is created within Docker’s storage directory on the host machine, and Docker manages that directory’s contents. Non-Docker processes on the Docker host or a Docker container can modify them at any time.
* `tmpfs mounts` are stored in the host system’s memory only and are never written to the host system’s file system. When the container stops, the tmpfs mount is removed, and files won’t persist.

## 14. Explain the purpose of the .dockerignore file.

Whenever a “docker builds” command is executed we see a line that tells uploading context. This refers to the creation of a .tar file by including all files in the directory where the Dockerfile is present and uploading them to docker daemon. Consider if we are putting Dockerfile in home directory entire files in your home and in all subdirectories would be included in the creation of a .tar file. Thus before updating the context docker daemon checks for the .dockerignore file. All files that match the data in the .dockerignore file would be neglected. Hence sensitive information is not sent to the Docker daemon.

sample .dockerignore file looks like this:
```sh
# comment
*/temp*
*/*/temp*
Temp?

```
This .dockerignore file ignores all files and directories whose names start with temp in any immediate subdirectory of the root or from any subdirectory that is two levels below the root. It also excludes files and directories in the root directory whose names are a one-character extension of temp. Everything that starts with # is ignored.

 