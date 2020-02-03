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

### 5. What are the container platforms available other than docker?

### 6. What are the basic actions performed on the docker container?

### 7. What are the disadvantages of Docker?

### 8. What is docker image?

### 9. What is the difference between ADD and COPY in Dockerfile?

### 10. What is the difference between ENTRYPOINT and CMD in Dockerfile?

### 11. How is Kubernetes related to Docker?

### 12. What is Docker Swarm?

### 13. Can you compare Chef with Docker?

### 14. How does Docker play a role in Continuous Integration?

### 15. Why Docker is chosen compared to other container platforms?