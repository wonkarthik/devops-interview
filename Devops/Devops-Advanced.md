### 1. What is CI/CD pipeline?
Continuous Integration is a development practice wherein developers regularly merge or integrate their code changes into a common shared repository very frequently (*).Every code check-in is then verified by automated build and automated test cases.

This approach helps to detect and fix the bugs early, improve software quality,reduce the validation and feedback loop time; hence increasing the overall product quality and speedy product releases.

(*) Unlike traditional SDLC process wherein a developer would wait until the completion of the code before he/she shares the work on the shared repository.
* Git becomes the best VCS tool with its strong, easy and reliable branching and merging architecture for Continuous Integration in a DevOps environment.
* Continuous Delivery is a software practice where every code check-in is automatically built, tested and ready for a release(delivery) to production.
* Every code check-in should be release/deployment ready.
* CD is an extension to CI.
* CD phase delivers the code to a production like environment such as dev, uat, preprod etc and run automated tests.
* On successful implementation of continuous delivery in the prod-like environment the code is ready to be deployed to the main production server.

### 2. How do you recover a deleted un-merged branch in your project source code?
By default, git does not allow you to delete a branch whose work has not yet been merged into the main branch.

 To see the list of branches not merged with the checked out branch run:
```sh
karthik@karthik:initialRepo [master] $git branch --no-merged
 dev
 --If you try to delete this branch, git displays a warning:

karthik@karthik:initialRepo [master] $git branch -d dev
error: The branch 'dev' is not fully merged.
If you are sure you want to delete it, run 'git branch -D dev'.
--If it is still deleted using the -D flag as:

karthik@karthik:initialRepo [master] $git branch -D dev
--See the references log information

karthik@karthik:initialRepo [master] $git reflog
cb9da2b (HEAD -> master) HEAD@{0}: checkout: moving from dev to master
b834dc2 (origin/master, origin/dev) HEAD@{1}: checkout: moving from master to dev
cb9da2b (HEAD -> master) HEAD@{2}: checkout: moving from master to master
cb9da2b (HEAD -> master) HEAD@{3}: checkout: moving from dev to master
b834dc2 (origin/master, origin/dev) HEAD@{4}: checkout: moving from master to dev
cb9da2b (HEAD -> master) HEAD@{5}: checkout: moving from uat to master
03224ed (uat) HEAD@{6}: checkout: moving from dev to uat
b834dc2 is the commit id when we jumped to ‘dev’ branch
Create a branch named ‘dev’ from this commit id again.

karthik@karthik:initialRepo [master] $git checkout -b dev b834dc2
Switched to a new branch 'dev'
karthik@karthik:initialRepo [dev]
```
### 3. Explain a good branching structural strategy that you have used for your project code development.
A good branching strategy is the one that adapts to your project and business needs. Every organization has a set of its own defined SDLC processes.
An example branching structural strategy that I have used in my project:
good-branching-structural-strategy

Diagram: Branching strategy
Clone the project available at github:
git clone http://github.com/divyabhushan/structuralStrategy.git structuralStrategy
 Guidelines: 

* “master-prod”: Accepts merges/code/commits only from the “prod” branch
* “prod”: Perform only a merge --squash from “release” branch.
* Merge only when approved by “QA”
* Tag every merge in the format: v1.0, v1.1 … v1.*
* “release”: merge from the branches “dev”, “uat”, “QA”.
* Every release commit/project code version has to be approved by “QA”.
* Tag every merge in the format: r1.0, r1.1 … r1.*
* “dev” and “uat” never merge with each other.
* “hotfix” branch commits are shared among any feature branches such as “dev” and “uat”
* “feature” branch is private to “dev” alone and is dropped after merging.
* CI/CD DevOps tools can be used to automate the above development and deployment to master_prod.
* Every project release: r1.0 .. r1.x on the ‘release’ branch can be tracked by Jenkins CI tool and will trigger a build, on a successful build continuous testing suite 
* cases will be triggered on the code. If the test passes the release will be delivered to ‘prod’ branch.
* Every source code delivered to ‘prod’ branch will be automatically deployed to ‘master_prod’ branch.

### 4. What is the key difference between a ‘git rebase’ and ‘git merge’
```sh
* ‘git merge’ takes the unique commits from the two branches, merge them together and create another commit with the merged changes; whereas in a ‘git rebase’ the work on the current branch is replayed and placed at the tip of the other branch resulting in re-writing the commit objects.
* ‘git rebase’ is applied from the branch to be rebased, whereas ‘git merge’ is applied on the branch that needs to merge the feature branch.
* ‘git merge’ preserves the history and makes it easier to track the ownership or when a code was broken in the project history; unlike ‘git rebase’ which changes the commit history by changing the commit objects (SHA-1) ids.
* ‘git rebase’ is often used locally for feature and quickfix and bug fix branches; however ‘git merge’ are used for long running stable branches.

```
### 5. How do you list the commits missing in your branch that are present in the remote tracking branch?

`git log --oneline <localBranch>..<origin/remoteBranch>`

Your local git branch should be set up to track a remote branch.

```sh
karthik@karthik:initialRepo [dev] $git branch -vv
* dev    b834dc2 [origin/dev] Add Jenkinsfile
 master b834dc2 [origin/master] Add Jenkinsfile
Reset ‘dev’ commit history to 3 commits behind using the command:



karthik@karthik:initialRepo [dev] $git reset --soft HEAD~3
karthik@karthik:initialRepo [dev] $git branch -vv
* dev    30760c5 [origin/dev: behind 3] add source code auto build at every code checkin using docker images
Compare and list the missing logs in local ‘dev’ branch that are present in ‘origin/dev’


karthik@karthik:initialRepo [dev] $git log --oneline dev..origin/dev
b834dc2 (origin/master, origin/dev, master) Add Jenkinsfile
c5e476c Rename 'prod' to 'uat'-break the build in Jenkings
6770b16 Add database logs.
Use ‘git pull’ to sync local ‘dev’ branch with the remote ‘origin/dev’ branch.
```
### 6. Define client level git hooks and their implementation.
Git hooks are the instruction scripts that gets triggered before(pre) or post(after) certain actions or events such as a git command run.

Git hooks can be implemented on both client(local machine) and server(remote) repositories.
Hooks objects are stored under .git/hooks directory. Hooks scripts are written in shell script and made executable.
--Code snippet of a ‘pre-commit’ script that stops the commit if a file has been deleted from the project:

```sh
#!/bin/sh
#Library includes:
. .git/hooks/hooks_library.lib
# An example hook script to verify what is about to be committed.
# Called by "git commit" with no arguments.  The hook should
# exit with non-zero status after issuing an appropriate message if
# it wants to stop the commit.
#Aim:Check for any Deleted file in the staging area, if any it stops you from commiting this snapshot.
set_variables 1 $0
if [ "$(git status --short | grep '^D')" ];then
        echo "WARNING!!! Aborting the commit. Found Deleted files in the Staging area.\n" | tee -a $LOGFILE
        echo "`git status --short | grep '^D' | awk -F' ' '{print $2}'`\n" | tee -a $LOGFILE
        exit 1;
else
        echo "[OK]: No deleted files, proceed to commit." | tee -a $LOGFILE
        exit 0;
fi
Scenario how I implemented the hooks scripts to enforce certain pre-commit and post-commit test cases:



Step 1: Running .git/hooks/pre-commit script.

[OK]: No deleted files, proceed to commit.
Thu Feb  7 12:10:02 CET 2019
--------------------------------------------
Step 2: Running .git/hooks/prepare-commit-msg script.

Get hooks scripts while cloning the repo. 
ISSUE#7092
Enter your commit message here.
README
code/install_hooks.sh
code/runTests.sh
database.log
hooksScripts/commit-msg
hooksScripts/hooks_library.lib
hooksScripts/post-commit
hooksScripts/pre-commit
hooksScripts/pre-rebase
hooksScripts/prepare-commit-msg
newFile
Thu Feb  7 12:10:02 CET 2019
--------------------------------------------
Step 3: Running .git/hooks/commit-msg script.

[OK]: Commit message has an ISSUE number
Thu Feb  7 12:10:02 CET 2019
--------------------------------------------
Step 4: Running .git/hooks/post-commit script.

New commit made:

1c705d3 Get hooks scripts while cloning the repo.
ISSUE#7092
A ‘pre-rebase’ script to stop the rebase on a ‘master’ branch:
Rebase ‘topic’ branch on ‘master’ branch


hooksProj [dev] $git rebase master topic
WARNING!!! upstream branch is master.
You are not allowed to rebase on master
The pre-rebase hook refused to rebase.
A code snippet demonstrating the use of a ‘pre-receive’ hook that is triggered just before a ‘push’ request on the Server, can be written to reject or allow the push operation.



localRepo [dev] $git push
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (2/2), 272 bytes | 272.00 KiB/s, done.
Total 2 (delta 0), reused 0 (delta 0)
remote: pre-recieve hook script
remote: hooks/pre-receive: [NOK]- Abort the push command
remote:
To /Users/Divya1/OneDrive/gitRepos/remoteRepo/
! [remote rejected] dev -> dev (pre-receive hook declined)
```
### 7. How do you create a new image in a container without using a dockerfile?

Install a new package in a container
```sh
docker run -it ubuntu
root@851edd8fd83a:/# which yum
--returns nothing
root@851edd8fd83a:/# apt-get update
root@851edd8fd83a:/# apt-get install -y yum
root@851edd8fd83a:/# which yum
/usr/bin/yum
--Get the latest container id
CONTAINER ID        IMAGE  COMMAND   CREATED STATUS                        PORTS NAMES
851edd8fd83a        ubuntu  "/bin/bash"   6 minutes ago Exited (127) 3 minutes ago                        
--base image changed
docker diff 851edd8fd83a
Commit the changes in the container to create a new image.

karthik@karthik:~ $docker commit 851edd8fd83a mydocker/ubuntu_yum
sha256:630004da00cf8f0b8b074942caa0437034b0b6764d537a3a20dd87c5d7b25179
--List if the new image is listed

karthik@karthik:~ $docker images
REPOSITORY            TAG IMAGE ID            CREATED SIZE
mydocker/ubuntu_yum   latest 630004da00cf        20 seconds ago 256MB
```
### 8. Create a docker file and build a new image. Run the image and create a container.
```sh
mkdir dockerFiles
vi dockerfile
FROM divyabhushan/myrepo:latest
COPY hello.sh /home/hello.sh
CMD ["bash", "/home/hello.sh"]
CMD ["echo", "Dockerfile demo"]
RUN echo "dockerfile demo" >> logfile
--Build an image from the dockerfile, tag the image name as ‘mydocker’

docker build -t mydocker dockerFiles/
docker build --```shtag <containerName> <dockerfile location>
karthik@karthik:~ $docker images
REPOSITORY            TAG IMAGE ID            CREATED SIZE
mydocker       ```sh       latest aacc2e8eb26a        20 seconds ago 88.1MB
karthik@karthik:~ $docker run mydocker
/home/karthik
Hello Divya
Bye Divya
View the images:
docker image
docker image --all
docker run -it ubuntu (imageName)
-i = interactive
-t = Allocate a sudo-tty (i.e, provide a terminal for the remote container image)
```
### 9. Develop your own custom test environment and publish it on the docker hub.
```sh
Write instructions in a dockerfile.

build the dockerfile and create an image in the registry
docker build -t learn_docker dockerFiles/

Create a container by running this image
docker run -it learn_docker

Push the container to the docker hub.
--Tag the local image as:

<hub-user>/<repo-name>:[:<tag>]
Examples:

docker tag learn_docker wonchaitanya/vote:dev
docker tag learn_docker wonchaitanya/vote:testing
--list the images for this container:
```sh
karthik@karthik:~ $docker images
REPOSITORY                  TAG IMAGE ID            CREATED SIZE
wonchaitanya/vote   develop 944b0a5d82a9        About a minute ago 88.1MB
learn_docker                dev1.1 944b0a5d82a9        About a minute ago 88.1MB
wonchaitanya/vote   dev d3e93b033af2        16 minutes ago 88.1MB
wonchaitanya/vote   testing d3e93b033af2        16 minutes ago 88.1MB
Push the docker images to docker hub
docker push wonchaitanya/vote:dev
docker push wonchaitanya/vote:develop
docker push wonchaitanya/vote:testing
The push refers to repository [docker.io/divyabhushan/ learn_docker]
53ea43c3bcf4: Pushed
4b7d93055d87: Pushed
663e8522d78b: Pushed
283fb404ea94: Pushed
bebe7ce6215a: Pushed
latest: digest: sha256:ba05e9e13111b0f85858f9a3f2d3dc0d6b743db78880270524e799142664ffc6 size: 1362

## custom test environment

* Image: Screenshot from the Docker hub.
* Docker hub repository: learn_docker has different variations of images. Image names are tagged.
* We can pull the tags from this repository as per the need.

## Summarize:

Develop your application code and all other dependencies like the binaries, library files, downloadables required to run the application in the test environment. Bundle it all in a directory.

* Edit the dockerfile to Run the downloadables and replicate the desired production environment as test env.
* Copy the entire application bundle to the test env in the docker container.
* Build the dockerfile and create new docker image and tag it.
* Push this docker image to the docker hub, which is now downloaded by other users to test.
NOTE: This docker image has your application bundle = application code + dependencies + test run time environment exactly similar to your machine. Your application bundle is highly portable with no hassles.
```
### 10. How do you prune data in a Docker?

Docker provides a system prune command to remove stopped containers and dangling images.Dangling images are the ones which are not attached to any container.

Run the prune command as below:

`docker system prune`

WARNING! This will remove:

all stopped containers
all networks not used by at least one container
all dangling images
all dangling build cache
Are you sure you want to continue? [y/N]

There is also a better and controlled way of removing containers and images using the command:
```sh
Step 1: Stop the containers

docker stop <container_id>
Step 2: Remove the stopped container

docker rm container_id
docker rm 6174664de09d
Step 3: Remove the images, first stop the container using those images and then

docker rmi <image_name>:[<tag>]
--give image name and tag

docker rmi ubuntu:1.0
--give the image id

docker rmi 4431b2a715f3
```

### 11. Explain Docker Orchestration

As the number of docker machines increases, there needs to be a system to manage them all. Docker Orchestration is a virtual docker manager and allows us to start, stop, pause, unpause or kill the docker nodes(machines).

### 12. How can Jenkins facilitate Deployment in a DevOps practice?
enkins auto-builds the source code from Git(any VCS) at every check-in; tests the source code and deploy the code in a tomcat environment via docker.

Webapp source code is then deployed by tomcat server on a production environment.

Pre-requisite:

Jenkins plugin: “Deploy to container” and “git plugin”
Edit the ‘post-build’ actions to include the tomcat details.
Under the SCM section add your git project repository url.
Git project structure:
```sh
karthik@karthik:myWeb [master] $
Dockerfile
   webapp/
       WEB-INF/
         classes/
         lib/
         web.xml
    index.jsp
--Dockerfile content:
vi Dockerfile
FROM tomcat:9.0.1-jre8-alpine
ADD ./webapp /usr/local/tomcat/webapps/webapp
CMD ["catalina.sh","run"]
Add a new project in Jenkins and track your git project url under SCM section.Have a dockerfile with the instruction to connect to the tomcat docker and deploy the webapp folder.

--Add the build section to ‘execute shell’ as below:

#!/bin/sh
echo "Build started..."
docker build -t webapp .
echo "Deploying webapp to tomcat"
docker run -p 8888:8080 webapp
echo http://localhost:8888/webapp
--Build the project from Jenkins:
```
### 13. How does Jenkins handle a failed test case?
* Pipelines artifacts
* Jenkins has built-in artifacts to record and capture the failures for analysis and investigation.
* This needs to be mentioned in the jenkinsfile pipeline:

Sample code:
```sh
Pipeline {
agent any
stages {
stage(‘Build’) {
steps {
sh ‘./test_suite1 build’
}
}
Stage(‘Test’) {
Steps {
sh ‘./test_suite1 test’
}
}
}
post {
always {
archiveArtifacts ‘build/libs/**/*.jar’
}
}
}
```
This gives the artifacts path and the filename

### 14. How to backup and restore Jenkins data and configurations
Backup of Jenkins is needed in case of disaster recovery, retrieving old configuration and for auditing.

$JENKINS_HOME folder keeps all the Jenkins metadata.

* That includes: build logs, job configs, plugins, plugin configurations etc.

Install the ‘think backup’ plugin in Jenkins and enable the backup from settings tab.We have to specify the backup directory and what we want to backup.

`Backup directory: $JENKINS_HOME/backup`

Backup files generated with the timestamp in the filenames will be stored under the path we specified.
```sh 
karthik@jenkins backup]$ pwd
/var/lib/Jenkins/backup
uat@jenkins backup]$ls
FULL-2019-02-4_07-14 FULL-2019-02-11_13-07

It is a good practice to version control (using Git) this back-up and move it to cloud.

## Restoring:

Backup files are in the tar+zip format.

Copy these over to another server; unzip and un-tar it on the server.

cd $JENKINS_HOME
tar xvfz /backups/Jenkins/backup-project_1.01.tar.gz
config.xml
jobs/myjob/config.xml
…
```