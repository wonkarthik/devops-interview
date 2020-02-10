## 1.How to fix a broken build for your project in Jenkins and how to make sure project build doesn't break in Jenkins at all?
The user needs to open the console output for the build and will try to see if any file changes that were missed during building the project. If there are no issues on that then a better approach would be clean and update his local workspace to replicate the problem on their local machine and will try to solve it.

To make sure Jenkins build is not broken at all we need to make sure that we perform a successful clean install on the local machine with all unit tests. Then make sure that all code changes are checked in without any issues. Then synchronize with a repository to make sure that all required config and changes and any differences are checked into the repository. 

## 2.What are the different ways in which build can be scheduled in Jenkins?
The below are the ways of scheduling build in Jenkins

* Builds can be triggered by source code management commits.
* Builds can be triggered sequentially after completion of other builds.
* Can be scheduled to run at a specified time using the CRON jobs
* Manual Build Requests.

## 3.Elaborate on how to move or copy Jenkins from one server to another?
Please follow the below steps,

* Slide a job from one installation of Jenkins to another by copying the related job directory
* Make a copy of an already existing job by making a clone of a job directory by a different name
* Renaming an existing job by renaming a directory.

## 4.What is the use of the Role Based strategy plugin?

The role-based strategy plugin allows us to create three different types of roles as describes below,

`Global Roles:` Some of the roles such as admin, job creator, anonymous can be created while selecting this option. The user can allow setting Overall, slave, job, and View and SCM permissions on a global basis.
`Project roles:` This job is basically to allow the creation of Job and Run permissions on a project basis.
`Slave roles:` This job is only to set node-related permissions.

## 5.What do you mean by build pipeline in Jenkins?

Creating a chain of jobs in Jenkins is the process of automatically starting the sequential job after one job is executed successfully. This approach lets the user build multi-step build pipelines or trigger the rebuild of a project if one of the Project dependencies is updated.

## 6.What do you mean by a Jenkins File and what are its advantages?

A Jenkins file is a text file that contains the definition of a Jenkins Pipeline and it is generally checked into source control. 

* Audit trail for the Pipeline can be monitored
* It serves as a single source of truth for the Pipeline, which can be viewed and edited by multiple members of the project.

## 7.What the Source Code Management tools does Jenkins support?
Jenkins supports a wide range of Source Code Management tools and few of them are mentioned below,

AccuRev
CVS
Subversion
Git
Mercurial
Perforce
Clearcase
RTC

## 8.What is the use of setting environment directive in Jenkins?

The environment directive specifies a sequence of key-value pairs which will be defined as environment variables for the all steps, or stage-specific steps, depending on where the environment directive is located within the Pipeline. This directive supports a special helper method `credentials()` which can be used to access pre-defined Credentials by their identifier in the Jenkins environment.

## 9.What are the Stages in Jenkins?

A stage block defines a conceptually distinct subset of tasks performed through the entire Pipeline. Stages contain a sequence of one or more stage directives, the stages section is where the bulk of the "work" described by a Pipeline will be located. Minimum, it is recommended that stages contain at least one stage directive for each discrete part of the continuous delivery process, such as Build, Test, and Deploy.

## 10.What is the programming language that Jenkins builds on?

Jenkins is an open source automation server written in Java. As an extensible automation server, Jenkins can be used as a simple CI server or turned into the continuous delivery hub for any project.

## 11.How can you define continuous delivery workflow?

Continuous delivery (CD ) is a product design methodology in which team software in short cycles, guaranteeing that the product can be dependably released whenever and, when releasing the product, doing as such manually. It goes for building, testing, and releasing programming with more prominent speed and recurrence. The methodology lessens the cost, time, and risk of conveying changes by considering progressively gradual updates to applications underway. A direct and repeatable arrangement procedure is significant for continuous delivery. CD stands out from continuous deployment, a comparable methodology where software is likewise delivered in short cycles yet through automatic arrangements as opposed to manual ones. The flowchart beneath demonstrates the Continuous Delivery Workflow. Hope it will be much easier to understand with visuals.

![](./jenkins/img/How-can-you-define-continuous-delivery-workflow.jpg)
