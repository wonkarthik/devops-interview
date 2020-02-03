### 1. What are containers?

A container is a standalone unit of software that packages together applications with all its dependencies and configurations. Containers help in abstracting applications from the computing environment in which they run.  Containers help in running the applications correctly in various computing environments. This helps to resolve the biggest problem “ It doesn’t work in my system. Multiple containers in an isolated manner can co-exist in a host system. All containers share the common host operating system. Thus making the containers lightweight and more preferable than virtual machines.

Detaching applications from its environment helps container-based applications to be deployed faster and on any on-premise systems or cloud. Developers with the help of containers can now concentrate on how to develop an application rather than how to run an application on different environments.IT operations teams focus on deployment and management rather than with application details such as specific software versions and configurations. Main competitors for containers are the virtual machines. Virtual machines are not lightweight compared to containers.

Containerization as a strategy is used as a practice across digital transformation due to its capability to bring a DevOps culture. The Dockerfile which defines the configuration of the image also acts as Infra as code up to some extent. Thus enabling teams to abstract their infrastructure in the form of code.
 
### 2. Why do we need containers? Give at least 3 reasons.
Containers is a virtualization technique which is based on the operating system resources. Multiple containers share the same host operating system. The main advantages are:

* Consistent and Reliable Environment
Containers help to achieve a stable environment that helps the application to run reliably. All dependencies like libraries etc needed for the applications are packaged into the containers. Containers can also include dependencies like specific versions of programming languages and other software libraries. Containers, in turn, increases the productivity of the developers and operations teams as they can concentrate more on the actual application rather than debugging different computing environments. This also creates fewer bugs for  the developers and IT operations teams.

* Run Anywhere
Containers can run anywhere-

1. Across different operating system whether it be a Linux, Windows or Mac system. or a developer  
2. Available across different service providers like AWS, Azure, GCP, etc.
3. Can be used across different environments like development, test, pre-production, production, etc.

* Isolation
Since the containers virtualize  OS resources like file systems developers get an isolated sandbox from other applications using containers. The container itself acts as a full-fledged system providing this isolation.

* From Code to Applications (Infrastructure As Code)
Applications along with its dependencies are packaged into the containers which could be versioned. This helps easy management and leading to greater agility and productivity.

### 3. Tell me the differences between virtual machines and containers?

|Sl No: | Virtual Machine | Containers|
|-------|-----------------|------------|
|1.| A virtualization technique where each VM has an individual operating system. |A virtualization technique where all containers share a host operating system.|
|2.| Virtual machines are isolated at the hardware level |Each container is isolated at the operating system level.|
|3.| Virtual machines take time to create |Containers are created fast |
|4.| Increased management overhead | Decreased management overhead as only one host operating system needs to be cared for.|
|5.| Takes minutes to boot up |Boots up in seconds |
|6.| Full isolation of VM’s  thus providing high security |Decreased security compared to VM’s|
|7.| VM’s are huge as it has the overhead of the separate OS |Lightweight as multiple containers share the same host operating system.
|8.|VM’s are mostly application-centric as making it service-centric is not a cost viable option. |Containers can be designed to be service-centric i.e a container for every microservice. Hence it is highly customizable for the performance of that particular microservices. Service-centric is the capability to configure a single service to run in a single container.|
|9.|Higher Cost |Less costly compared to VM’s|
|10.| More effort required to make VM’s versionable.|Easily Versionable|
|11.| The marketplace in VM’s are segmented and not well known as Docker Hub |Availability of a central, trusted  marketplace where we can pull the images for the containers|

### 4. What is Docker?

Docker is an open source container platform, from Docker, Inc launched in 2013. Docker enables operating system level virtualization. Docker is available as free and enterprise version. Docker containers, unlike Virtual Machines, share the same host operating system. It has less overhead compared to VM’s. Docker created the industry guidelines for containers, making it more portable and secure. Docker is having the best isolation capability that is beneficial when more containers run on the same host system. Docker could be run on any system on-premises or cloud. Using docker we can create, start, stop, move, pause, unpause or delete a container.

A Docker container can connect to one or more networks, attach storage to it, and even create a new image depending on the current state of the container. Docker also helps in controlling how the containers are isolated from other containers or host machine. Dockerfile in docker platform helps to achieve infrastructure as code thus enabling consistent and reliable computing environment. This reduces a lot of configuration and infrastructure related defects that would improve the quality of applications.  

Docker is supported on all the latest technologies like Google Cloud Platform and by Google Kubernetes Engine. Docker was primarily developed for Linux systems but now also supports Windows and Mac Os.

### 5. What are the container platforms available other than docker?
According to 2018 reports Docker is the most popular container platform constituting 83% of container space. It’s preferred because of the ease with which the user can work with. Docker also provides greater isolation between the containers on the same host operating system. Few of the other popular ones are CoreOS rkt

12 percent of production containers were rkt (pronounced “Rocket”) from CoreOS. rkt supports two types of images: Docker and appc. The main advantage of rkt is its usage with Kubernetes.In Kubernetes, an rkt container runtime can be declared as

`“ kubelet --container-runtime=rkt”.`
Compared with Docker, rkt has fewer third-party integrations. Red Hat recently acquired CoreOS. Application Container Image or appc is defined by the App Container spec. An ACI contains all necessary files to execute an application and an image.

`Mesos Containerizer`
4 percent of production containers were Mesos from Apache. Mesos supports both Docker and appc image types. It works better with the frameworks of big data applications.  Mesos is not a standalone container and requires the Mesos framework to make it run.

`LXC Linux Containers`
1 percent of containers were LXC Linux Containers. LXC also has a great active community around it. The main disadvantage of LXC is its incompatibility with Kubernetes.

### 6. What are the basic actions performed on the docker container?
Basic actions on Docker containers are:

* Create a docker container
Following command creates the docker container with the required images.

`docker create --name <container-name> <image-name>`
* Run docker container
Following command helps  to execute the container

`docker run -it -d --name <container-name> <image-name> bash`
Main steps involved in the run command is

Pulls the image from Docker Hub if it’s not present in the running environment
1. Creates the container.
2. The file system is allocated and is mounted on a read/write layer.
3. A network interface is allocated that allows docker to talk to the host.
4. Finds an available IP address from pool.
5. Runs our application in our case “bash” shell.
6. Captures application outputs

*  Pause container
 Processes running inside the container is paused. Following command helps us to achieve this.

`docker pause <container-id/name>`
Container can’t be removed if in a paused state.

* Unpause container
Unpause moves the container back to run the state. Below command helps us to do this.

`docker unpause <container-id/name>`
* Start container
If container is in a stopped state, container is started.

`docker start <container-id/name>`
* Stop container
Container with all its processes is stopped with below command.

`docker stop <container-id/name>`
To stop all the running Docker containers use the  below command

`docker stop $(docker ps -a -q)`
* Restart container
Container along with its processes are restarted

`docker restart <container-id/name>`
* Kill container
A container can be killed with below command

`docker kill <container-id/name>`
* Destroy container
The entire container is discarded. It is preferred to do this when the container is in a stopped state rather than do it forcefully.

`docker rm <container-id/name>`

### 7. What are the disadvantages of Docker?
Like any other technologies, Docker has its own advantages and disadvantages. Docker is not a silver bullet and needs care in architecting and orchestrating docker containers keeping below points in mind.

* `Docker` is not so fast compared to bare metal servers. Even though containers are lightweight and so easy to boot up it is subjected to decreased network performance. Performance of the containers are affected as a single operating system caters to multiple containers.
* `Integrability:` Even though Docker is open source some of the container products don’t work with all. This may be due to the competition prevailing in the market. For eg:  OpenShift which is a container-as-a-service platform from Red Hat, only works with the Kubernetes.
* `Data loss in containers:` When the container exits the data will be lost. Data could be saved through volume mounting like Docker Data Volumes but more hard work needs to be done in this space.
* `Poor or no GUI:` Containers were used mainly for deploying server application that doesn't require GUI. There are some methods to run GUI app inside containers but it’s somewhat clumsy these days.
* `More suitable for applications that use microservices:` Generally the docker’s benefit is to ease the application delivery by providing a packaging mechanism but the true benefit comes when we use microservices.
There are other virtualization techniques like unikernels which is based on library operating systems. Unikernels provide improved security, more optimisation and boots faster.

### 8. What is docker image?
A Docker image is an executable file, that creates a Docker container. An image is built from the executable version of an application together with its dependencies and configurations. Running instance of an image is a container.

Docker image includes system libraries, tools, and other files and dependencies for the application. An image is made up of multiple layers. Layered structure helps the developers to reuse already available static image layers from the Docker Hub, for different projects. This saves the developers time. Each image has a base layer which could be already available in Docker Hub or built from scratch. Then a readable/writable layer over the static layer is created that helps to customise the container. Each layer of the docker image could be verified in /var/lib/docker/aufs/diff, or via the Docker history command.

Storage drivers are used to managing the image layers. A writable layer created while creating the container is called the container layer. Multiple containers can share the same base layer and have their own writable layer. Few commands that can be used with images are:

* docker history shows the history of an image and its layers.
* docker update helps to update the container configurations.
* docker tag creates a tag for the container and organizes container images.
* docker search looks for the image in Docker Hub
* docker save allows saving of images.
* docker rmi removes one or multiple images.

### 9. What is the difference between ADD and COPY in Dockerfile?

The ADD instruction is used to copy files from build context to the image. Apart from regular files, it also allows URL and archive (tar, gzip, etc) files as the <source> parameter.

When a URL is provided, a file is downloaded from the URL and copied to the <destination>.It automatically unpacks compressed files, if the <source> argument is a local file in a recognized compression format (tar, gzip, bzip2, etc).

Whereas COPY does a straight-forward, as-is copy of files and folders from the build context into the container. It doesn't support URLs or gives any special treatment to archives.

Anything that you want to COPY into the container must be present in the local build context. The recommendation from the Docker team is to use COPY in almost all cases.

### 10. What is the difference between ENTRYPOINT and CMD in Dockerfile?

ENTRYPOINT is a definition in Dockerfile that specifies a command that will always be executed when the container starts. It allows us to configure a command along with its parameters that will run as an executable in a container. Even if we do not specify any ENTRYPOINT, we may inherit it from the base image specified using the FROM keyword in your Dockerfile. Most of the official Docker base images have an ENTRYPOINT of /bin/sh or /bin/bash.

To override the ENTRYPOINT at runtime, we can use --entrypoint.

Whereas, the main purpose of a CMD is to provide defaults for an executing container. These defaults can include an executable or for executing an ad-hoc command in a container. It can omit the executable as well, in which case we must specify an ENTRYPOINT instruction as well specified with the JSON array format.

The thumb rule is that every Dockerfile should specify at least one of CMD or ENTRYPOINT commands.

### 11. How is Kubernetes related to Docker?

Though Docker is a great tool for quick development and environment setup, it cannot be used in production directly. It needs a layer of orchestration for container management.

That’s where Kubernetes comes into the picture. Kubernetes is an open source orchestration system for Docker containers. It manages containerized applications across multiple hosts and provides basic mechanisms for deployment, maintenance, and scaling of applications.

In a Kubernetes environment, one or more docker containers are run inside a ‘pod’. A pod is a basic unit that Kubernetes deals with and it represents one or more containers that should be controlled as a single "application".

### 12. What is Docker Swarm?

Swarm is the native way provided by docker for implementing clusters for Docker containers. Basically, it allows a group of machines that are running Docker to join as a single cluster. Docker commands executed on a cluster are implemented on all the hosts by the swarm manager. Machines in a swarm can either be physical or virtual. After joining a swarm, they are referred to as nodes.

It also provides built-in load balancing and Service Discovery for the applications running as containers.

However, a more popular way of implementing clustering in production is achieved using Kubernetes. It is technically superior to Docker Swarm as it provides better health checking provisions at the pod level, better container scheduling and scaling.

### 13. Can you compare Chef with Docker?

A Chef is a Configuration Management tool which is primarily used by admins and DevOps teams to manage the application environments, web-server configuration, databases, and load balancers. Instructions are created in the form of recipes, that defines the components in the infrastructure and how those components can be deployed, configured and managed. In a nutshell, it makes it automates the rolling out of changes to various environments or machines.

Whereas Docker is a way to package code into consistent units of work known as containers. These units of work can then be deployed to development, QA and production environments with far greater ease and consistency.

### 14. How does Docker play a role in Continuous Integration?

Continuous Integration is a mechanism used to enable DevOps methodology in development process i.e. helps to obtain immediate feedback on the changes made in the code by executing unit and acceptance tests, as soon as the code changes are checked-in to the repository.

A typical process involves developers merging their changes to the branch as often as possible and they are validated by building, running and executing automated tests against the application.

Docker plays a key role in enabling CI, as it can seamlessly integrate with any popular CI tools like Jenkins or TeamCity. Also, several Gradle plugins like Palantir, trans mode are available for automating the docker build and publish images by creating respective Gradle tasks.

We can then define stages in Jenkins to perform tasks like building Docker images, starting containers using compose and executing Acceptance tests to validate the application changes.

### 15. Why Docker is chosen compared to other container platforms?

* Docker has a huge user base in comparison with its competitors. According to a recent report, by an infrastructure monitoring tool company, it claims 25 % of companies are already using containerization technology. Out of which 83 % of containers are docker containers. This user base includes big banks, manufacturing companies, large product firms, etc.  Hence Docker is the most popular option to choose from the existing container technologies.
* Docker being an open source technology enjoys huge community support and hence it has been the most preferred containerization tool in the industry. Docker was primarily developed for Linux but now supports Windows and Mac OS environments.
* There is no container limitation on running Docker as the underlying could be anything like a laptop/cloud system. It only depends on the host system’s OS resources.
* Docker HUB forms the repository of docker images. Docker Hub users can upload and download the docker images. A lot of applications are released as docker images which enable reusability and interoperability.  
* Docker is extensively documented technology available in the containerization space. That makes it easy for even the first time users to work with it.
* Docker works very well with  Google Cloud Platform and by Google Kubernetes Engine and across different cloud platform providers.  
* Docker is also well supported by other configuration management platforms like Chef, Ansible, Puppet, etc