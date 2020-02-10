### 1.what is Devops ?
DevOps is an approach to collaborate the development and operations teams for a better, bug-free continuous delivery and integration of the source code.
DevOps is about automating the entire SDLC (Software Development Life Cycle) process with the implementation of CI/CD practices.

CI/CD are the Continuous integration and continuous deployment methodologies.
Every source code check-in will automatically build and unit test the entire code against a
production like environment and continuously deployed to production environment after it passes its automated tests.
That eliminates the long feedback, bug-fix, and product enhancements loops between every
Release.

Every team takes the accountability of the entire product right from the requirement analysis to documentation to coding, testing in development environments, code deployment and continuous improvements in terms of bugs and feedback from reviewers and the customers.

### 2. What are some of the tools you have used in DevOps approach?
Considering DevOps to be an ideology towards achieving a quality product, every organization has its own guidelines and approach towards it.
Some of the popular tools I have used are:

* Git as a Distributed VCS to manage the source code.
* Jenkins to achieve CI/CD (Continuous Integration and Continuous Delivery) +plugins
* Puppet for Configuration Management and Deployment tool
* Nagios for Continuous Monitoring; and
* Docker for containerization.
 
### 3. What is Git?

Git is a Distributed Version Control System; used to logically store and backup the entire history of how your project source code has developed, keeping a track of every version change of the code.

Git facilitates very flexible and efficient branching and merging of your code with other collaborators.Being distributed git is extremely fast and more reliable as every developer has his own local copy of the entire repository.

Git allows you to undo the mistakes in the source code at different tiers of its architecture namely- Working directory, Staging (Index) area, Local repository, and Remote repository.

Using Git we can always get an older version of our source code and work on it.Git tracks every bit of data as it checksums every file into unique hash codes referring to them via pointers

### 4. What is a git commit object? How is it read?

When a project repository is initialized to be a git repository, git stores all its metadata in a hidden folder “.git” under the project root directory.
Git repository is a collection of objects. 

Git has 4 types of objects – blobs, trees, tags, and commits.

Every commit creates a new commit object with a unique SHA-1 hash_id.
Each commit object has a pointer reference to the tree object, its parent object, author, committer and the commit message.
```sh
To see the commit log message along with the textual diff of the code, run:
git show <commit_id>


karthik@karthikinitialRepo [master] $git show f9354cb
commit f9354cb08d91e80cabafd5b54d466b6055eb2927
Author: wonkarthik <wonkarthik@hotmail.com>
Date:   Mon Feb 11 23:39:24 2019 +0100
    Add database logs.
diff --git a/logs/db.log b/logs/db.log
new file mode 100644
index 0000000..f8854b0
--- /dev/null
+++ b/logs/db.log
@@ -0,0 +1 @@
+database logs

To read a commit object git has ‘git cat-file’ utility.

karthik@karthikinitialRepo [master] $git cat-file -p f9354cb
tree 2a85825b8d20918350cc316513edd9cc289f8349
parent 30760c59d661e129329acfba7e20c899d0d7d199
author wonkarthik <wonkarthik@hotmail.com> 1549924764 +0100
committer wonkarthik <wonkarthik@hotmail.com> 1549924764 +0100 
Add database logs.

A tree object is like an OS directory that stores references to other directories and files (blob type).

karthik@karthikinitialRepo [master] $git cat-file -p 2a85825b8d20918350cc316513edd9cc289f8349
100755 blob 054acd444517ad5a0c1e46d8eff925e061edf46c README.md
040000 tree dfe42cbaf87e6a56b51dab97fc51ecedfc969f39 code
100644 blob e08d4579f39808f3e2830b5da8ac155f87c0621c dockerfile
040000 tree 014e65a65532dc16a6d50e0d153c222a12df4742   logs
```
### 5. What is the difference between a git reset and a git revert.

* git revert is used to record some new commits to reverse the effect of some earlier commits/snapshot of a project.
* Instead of removing the commit from the project history, it figures out how to undo the changes introduced by the commit & appends a new commit with the resulting content in the current branch.

`Usage: git revert <commit_id>`

`Use:` To undo an entire commit from your project history; removing a bug introduced by a commit.
### Reset Vs Revert

* git “reset”: resets the project to a previous snapshot erasing the changes.
* git “revert” does not change the project history unlike git “reset”
* Git “revert” undoes the commit id changes and applies the undo work as a new commit id object.

### 6. How does ‘git rebase’ work? When should you rebase your work instead of a ‘git merge’?

There are scenarios wherein one would like to merge a quickfix or feature branch with not a huge commit history into another ‘dev’ or ‘uat’ branch and yet maintain a linear history.

A non-fast forward ‘git merge’ would result in a diverged history. Also when one wants the feature merged commits to be the latest commits; ‘git rebase’ is an appropriate way of merging the two branches.

‘git rebase’ replays the commits on the current branch and place them over the tip of the rebased branch.Since it replays the commit ids, rebase rewrites commit objects and create a new object id(SHA-1). Word of caution: Do not use it if the history is on release/production branch and being shared on the central server. Limit the rebase on your local repository only to rebase quickfix or feature branches.

Steps:

Say there is a ‘dev’ branch that needs a quick feature to be added along with the test cases from ‘uat’ branch.

`Step 1:` Branch out ‘new-feature’ branch from ‘dev’.
Develop the new feature and make commits in ‘new-feature’ branch.
```sh
[dev ] $git checkout -b new-feature
[new-feature ] $ git add lib/commonLibrary.sh && git commit -m “Add commonLibrary file”
karthik@karthik:rebase_project [new-feature] $git add lib/commonLibrary.sh && git commit -m 'Add commonLibrary file'karthik@karthik:rebase_project [new-feature] $git add feature1.txt && git commit -m 'Add feature1.txt'
karthik@karthik:rebase_project [new-feature] $git add feature2.txt && git commit -m 'Add feature2.txt'
```
`Step 2:` Merge ‘uat’ branch into ‘dev’
```sh
[dev] $ git merge uat
```
`Step 3:` Rebase ‘new-feature’ on ‘dev’
```sh
karthik@karthik:rebase_project [dev] $git checkout new-feature
karthik@karthik:rebase_project [new-feature] $git rebase dev
First, rewinding head to replay your work on top of it...
Applying: Add commonLibrary file
Applying: Add feature1.txt
Applying: Add feature2.txt
```
`Step 4:` Switch to ‘dev’ branch and merge ‘new-feature’ branch, this is going to be a fast-forward merge as ‘new-feature’ has already incorporated ‘dev’+’uat’ commits.
```sh
karthik@karthik:rebase_project [new-feature] $git checkout dev
karthik@karthik:rebase_project [dev] $git merge new-feature
Updating 5044e24..3378815
Fast-forward
feature1.txt         | 1 +
feature2.txt         | 1 +
lib/commonLibrary.sh | 16 ++++++++++++++++
3 files changed, 18 insertions(+)
create mode 100644 feature1.txt
create mode 100644 feature2.txt
create mode 100644 lib/commonLibrary.sh
this will result in linear history with ‘new-feature’ results being at the top and ‘dev’ commits being pushed later.
```
`Step 5:` View the history of ‘dev’ after merging ‘uat’ and ‘new-feature’ (rebase)
```sh
karthik@karthik:rebase_project [dev] $git hist
* 3378815 2019-02-14 | Add feature2.txt (HEAD -> dev, new-feature) [karthik chaitanya]
* d3859c5 2019-02-14 | Add feature1.txt [karthik chaitanya]
* 93b76f7 2019-02-14 | Add commonLibrary file [karthik chaitanya]
*   5044e24 2019-02-14 | Merge branch 'uat' into dev [karthik chaitanya]
|\  
| * bb13fb0 2019-02-14 | End of uat work. (uat) [karthik chaitanya]
| * 0ab2061 2019-02-14 | Start of uat work. [karthik chaitanya]
* | a96deb1 2019-02-14 | End of dev work. [karthik chaitanya]
* | 817544e 2019-02-14 | Start of dev work. [karthik chaitanya]
|/  
* 01ad76b 2019-02-14 | Initial project structure. (tag: v1.0, master) [karthik chaitanya]
* NOTE: ‘dev’ will show a diverged commit history for ‘uat’ merge and a linear history for ‘new-feature’ merge.
```
### 7. What is a Docker? Explain its role in DevOps.
Every source code deployment needs to be portable and compatible on every device and environment.

Applications and their run time environment such as libraries and other dependencies like binaries, jar files, configuration files etc.. are bundled up(packaged) in a Container.

Containers as a whole are portable, consistent and compatible with any environment.

In development words, a developer can run its application in any environment: dev, uat, preprod and production without worrying about the run-time dependencies of the application.
```sh
Docker is a container platform.
Docker is a framework that provides an abstraction layer to manage containers.
Docker is a containerization engine, which automates packaging, shipping, and deployment of any software applications or Containers.
Docker also lets us test the code and then deploy it in production.
Docker along with Jenkins (a Continuous Integration tool) and Git plugin contributes in CI/CD process.
```
### 8. What is a Docker image? How are they shared and accessed?

A developer writes code instructions to define all the applications and its dependencies in a file called a “Dockerfile”.Dockerfile is used to create a ‘Docker image’ using the ‘docker build <directory>’ command.The build command is run by the docker daemon.

When you run a Docker image “Containers” are created. Containers are runtime instances of a Docker image.
```sh
A Container can have many images.
Docker containers are stored in a docker registry on docker host.
Docker has a client-Server architecture.
Docker registry is generally pushed and shared on a Docker hub (Remote server).
```

### 9. How do you work on a container image?

--Get docker images from docker hub or your docker repository
```sh
docker pull busybox
docker pull centos
docker pull wonchaitanya/vote
karthik@karthik:~ $docker pull wonchaitanya/vote
Using default tag: latest
latest: Pulling from wonchaitanya/vote
6cf436f81810: Pull complete
987088a85b96: Pull complete
b4624b3efe06: Pull complete
d42beb8ded59: Pull complete
d08b19d33455: Pull complete
80d9a1d33f81: Pull complete
Digest: sha256:c82b4b701af5301cc5d698d963eeed46739e67aff69fd1a5f4ef0aecc4bf7bbf
Status: Downloaded newer image for wonchaitanya/vote:latest
--List the docker images

karthik@karthik:~ $docker images
REPOSITORY            TAG IMAGE ID            CREATED SIZE
wonchaitanya/vote   latest 72a21c221add        About an hour ago 88.1MB
busybox               latest 3a093384ac30        5 weeks ago 1.2MB
centos                latest 1e1148e4cc2c        2 months ago 202MB
--Create a docker container by running the docker image

--pass a shell argument  : `uname -a`

karthik@karthik:~ $docker run centos uname -a
Linux c70fc2da749a 4.9.125-linuxkit #1 SMP Fri Sep 7 08:20:28 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
--Docker images can be built by reading a dockerfile
dockerfile

--build a new image : ‘newrepo’ with tag:1.0 from the dockerFiles/dockerfile

# docker build -t newrepo:1.1 dockerFiles/

dockerfile

--Now create a container from the above image:
# docker run --name centos -it centos:1.1

dockerfile

--List all the containers
# docker ps -a
dockerfile

--start the container
# docker start nldfrerr3843743

dockerfile

--List only the running containers
# docker ps 

```

### 10. What is Puppet? What is the need for it?
Puppet is a Configuration Management and deployment tool for administrative tasks.

This tool helps in automating the provisioning, configuration, and management of Infrastructure and Systems.

In simple words:

Puppet helps administrators to automate the process of manually creating and configuring Virtual machines.
Say, you have to bring up ‘n’ number of Servers with ‘x’ number of VMs(Virtual machines) on them. Each VM needs to be configured for certain users, groups, services, applications, and databases etc.
The entire infrastructure can be loaded up with the help of Puppet programs re-using the codes on multiple servers.
Key feature: Idempotency
The same set of configurations can be run multiple times to build a machine on a server, as puppet would allow only unique configurations to be run.

### 11. What does ‘Infrastructure as code’ means in terms of Puppet?
Entire Server Infrastructure setup configurations are written in terms of codes and re-used on all the Puppet Server agent’s nodes(machines) that are connected via a Puppet master Server.

This is achieved by the use of code snippets called ‘manifests’; that are configuration files for every Server agent node.

Each manifest (program files with *.pp extension) consists of the resources and the codes.
We can review, deploy and test the environment configuration for development, testing and production environments.
Puppet manifests written once are deployed on any environment to build up the same infrastructure.

### 12. What is Jenkins used for in DevOps?

Jenkins is a self-contained, open source automation server(tool) for continuous development.

Jenkins aids and automates CI/CD process.

It gets the checked in code from VCS like Git using a ‘git plugin’, build the source code, run test cases in a production-like environment and make the code release ready using ‘deploy’ plugin.

These continuous delivery pipelines are written in a ‘Jenkinsfile’ which is also checked into project’s source code and version controlled by Git.
Pipelines are a continuous set of jobs that are run for continuous delivery and these jobs are integrated at every section of the workflow.
Jenkins pipelines easily connect to Docker images and containers to run inside.
Pipelines easily provide the desired test environment without having to configure the various system tools and dependencies.
Sample Jenkins file
```sh
pipeline {
   agent { docker { image 'ubuntu:latest' } }
   stages {
       stage('build') {
           steps {
               sh 'uname -a'
           }
       }
   }
}
```
* Jenkins will start the container- ubuntu with the latest image and execute the test case steps inside it.
* agent directive says where and how to execute the pipeline
* jenkinsfile (declarative pipeline)
* pipeline
* Jenkins saves us the trouble of debugging after a huge commit history if there was a code break.

### 13. How do you implement CI/CD using Jenkins?
* Continuous Integration using Jenkins and Git plugin
* Create a new Jenkins item as a ‘Pipeline’.
* Add ‘Git’ as branch source and give the Project repository url.
* Every time source code is pushed to the remote git repository from the local git repo;
* Jenkins job gets started(triggered).
* Jenkins build the code and the output is available under “console output”
* In the git repository add a ‘jenkinsfile’ commit and push the code to git repository.
* Add a Jenkinsfile in the git repository.

```sh
pipeline {
   agent { docker { image 'ubuntu:latest' } }
   stages {
       stage('build') {
           steps {
               sh 'uname -a'
           }
       }
stage('Test') {
           steps {
               sh './jenkins/scripts/test.sh'
           }
       }
   }
}
```
### 14. Mention some post condition pipelines options that you used in Jenkinsfile?

We can mention some test conditions to run post the completion of stages in a pipeline.

Code snippet
```sh
post {
always {
echo “This block runs always !!!”
}
success {
echo “This block runs when the stages has a success status”
}
unstable {
echo “This block is run when the stages abort with an unstable status”
}
}
```
Here are the post conditions reserved for jenkinsfile:

* always:
Run the steps in the post section regardless of the completion status of the Pipeline’s or stage’s run.

* unstable:
Only run the steps in post if the current Pipeline’s or stage’s run has an "unstable" status, usually caused by test failures, code violations, etc.

* aborted:
Only run the steps in post if the current Pipeline’s or stage’s run has an “aborted” status.

* success:
Only run the steps in post if the current Pipeline’s or stage’s run has a "success" status.

* failure:
Only run the steps in post if the current Pipeline’s or stage’s run has a "failed" status.

* changed:
Only run the steps in post if the current Pipeline’s or stage’s run has a different completion status from its previous run.

* cleanup:
Run the steps in this post condition after every other post condition has been evaluated, regardless of the Pipeline or stage’s status.

