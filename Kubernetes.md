## 1 What is the difference between config map and secret? (Differentiate the answers as with examples)

Config maps ideally stores application configuration in a plain text format whereas Secrets store sensitive data like password in an encrypted format. Both config maps and secrets can be used as volume and mounted inside a pod through a pod definition file.
```sh
Config map:

                 kubectl create configmap myconfigmap
 --from-literal=env=dev
Secret:

echo -n ‘admin’ > ./username.txt
echo -n ‘abcd1234’ ./password.txt
kubectl create secret generic mysecret --from-file=./username.txt --from-file=./password.txt
```

## 2.If a node is tainted, is there a way to still schedule the pods to that node?
When a node is tainted, the pods don't get scheduled by default, however, if we have to still schedule a pod to a tainted node we can start applying tolerations to the pod spec.
```sh
Apply a taint to a node:

kubectl taint nodes node1 key=value:NoSchedule
Apply toleration to a pod:

spec:
tolerations:
- key: "key"
operator: "Equal"
value: "value"
effect: "NoSchedule"
```
## 3.Can we use many claims out of a persistent volume? Explain?
The mapping between persistentVolume and persistentVolumeClaim is always one to one. Even When you delete the claim, PersistentVolume still remains as we set persistentVolumeReclaimPolicy is set to Retain and It will not be reused by any other claims. Below is the spec to create the Persistent Volume.

```sh
apiVersion: v1
kind: PersistentVolume
metadata:
name: mypv
spec:
capacity:
storage: 5Gi
volumeMode: Filesystem
accessModes:
- ReadWriteOnce
persistentVolumeReclaimPolicy: Retain
```
## 4.What kind of object do you create, when your dashboard like application, queries the Kubernetes API to get some data?
You should be creating serviceAccount. A service account creates a token and tokens are stored inside a secret object. By default Kubernetes automatically mounts the default service account. However, we can disable this property by setting automountServiceAccountToken: false in our spec. Also, note each namespace will have a service account
```sh
apiVersion: v1
kind: ServiceAccount
metadata:
name: my-sa
automountServiceAccountToken: false
```
## 5. What is the difference between a Pod and a Job? Differentiate the answers as with examples)
A Pod always ensure that a container is running whereas the Job ensures that the pods run to its completion. Job is to do a finite task.
```sh
Examples:

kubectl run mypod1 --image=nginx --restart=Never
kubectl run mypod2 --image=nginx --restart=onFailure
○ → kubectl get pods
NAME           READY STATUS   RESTARTS AGE
mypod1         1/1 Running   0 59s
○ → kubectl get job
NAME     DESIRED SUCCESSFUL   AGE
mypod1   1 0            19s
```
## 6.How do you deploy a feature with zero downtime in Kubernetes?
y default Deployment in Kubernetes using RollingUpdate as a strategy. Let's say we have an example that creates a deployment in Kubernetes
```sh
kubectl run nginx --image=nginx # creates a deployment
○ → kubectl get deploy
NAME    DESIRED  CURRENT UP-TO-DATE   AVAILABLE AGE
nginx   1  1 1            0 7s
Now let’s assume we are going to update the nginx image

kubectl set image deployment nginx nginx=nginx:1.15 # updates the image 
Now when we check the replica sets

kubectl get replicasets # get replica sets
NAME               DESIRED CURRENT READY   AGE
nginx-65899c769f   0 0 0       7m
nginx-6c9655f5bb   1 1 1       13s
From the above, we can notice that one more replica set was added and then the other replica set was brought down

kubectl rollout status deployment nginx 
# check the status of a deployment rollout

kubectl rollout history deployment nginx
 # check the revisions in a deployment

○ → kubectl rollout history deployment nginx
deployment.extensions/nginx
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```
## 7. How to monitor that a Pod is always running?
We can introduce probes. A liveness probe with a Pod is ideal in this scenario.

A liveness probe always checks if an application in a pod is running,  if this check fails the container gets restarted. This is ideal in many scenarios where the container is running but somehow the application inside a container crashes.
```sh
spec:
containers:
- name: liveness
image: k8s.gcr.io/liveness
args:
- /server
livenessProbe:
      httpGet:
        path: /healthz
```
## 8. What are the types of multi-container pod patterns? (Explain each type with examples)
sidecar:
A pod spec which runs the main container and a helper container that does some utility work, but that is not necessarily needed for the main container to work.

adapter:
The adapter container will inspect the contents of the app's file, does some kind of restructuring and reformat it, and write the correctly formatted output to the location.

ambassador:
It connects containers with the outside world. It is a proxy that allows other containers to connect to a port on localhost.

## 9. What is the difference between replication controllers and replica sets?
The only difference between replication controllers and replica sets is the selectors. Replication controllers don't have selectors in their spec and also note that replication controllers are obsolete now in the latest version of Kubernetes.
```sh
apiVersion: apps/v1
kind: ReplicaSet
metadata:
name: frontend
labels:
app: guestbook
tier: frontend
spec:
# modify replicas according to your case
replicas: 3
  selector:
    matchLabels:
      tier: frontend
template:
metadata:
labels:
tier: frontend
spec:
containers:
- name: php-redis
image: gcr.io/google_samples/gb-frontend:v3
```
## 10. How do you tie service to a pod or to a set of pods?
By declaring pods with the label(s) and by having a selector in the service which acts as a glue to stick the service to the pods.
Let's say if we have a set of Pods that carry a label "app=MyApp" the service will start routing to those pods.

```sh
kind: Service
apiVersion: v1
metadata:
name: my-service
spec:
  selector:
    app: MyApp
ports:
- protocol: TCP
port: 80
```

## 11. Having a Pod with two containers, can I ping each other? like using the container name?
Containers on same pod act as if they are on the same machine. You can ping them using localhost:port itself. Every container in a pod shares the same IP. You can `ping localhost` inside a pod. Two containers in the same pod share an IP and a network namespace and They are both localhost to each other. Discovery works like this: Component A's pods -> Service Of Component B -> Component B's pods and Services have domain names servicename.namespace.svc.cluster.local, the dns search path of pods by default includes that stuff, so a pod in namespace Foo can find a Service bar in same namespace Foo by connecting to `bar`

## 12. Does the rolling update with state full set replicas =1 makes sense?
No, because there is only 1 replica, any changes to state full set would result in an outage. So rolling update of a StatefulSet would need to tear down one (or more) old pods before replacing them. In case 2 replicas, a rolling update will create the second pod, which it will not be succeeded, the PD is locked by first (old) running pod, the rolling update is not deleting the first pod in time to release the lock on the PDisk in time for the second pod to use it. If there's only one that rolling update goes 1 -> 0 -> 1.f the app can run with multiple identical instances concurrently, use a Deployment and roll 1 -> 2 -> 1 instead.

## 13. Different Ways to provide API-Security on Kubernetes?
Use the correct auth mode with API server authorization-mode=Node,RBAC Ensure all traffic is protected by TLS Use API authentication (smaller cluster may use certificates but larger multi-tenants may want an AD or some OIDC authentication).

Make kubeless protect its API via authorization-mode=Webhook. Make sure the kube-dashboard uses a restrictive RBAC role policy Monitor RBAC failures Remove default ServiceAccount permissions Filter egress to Cloud API metadata APIs Filter out all traffic coming into kube-system namespace except DNS.

A default deny policy on all inbound on all namespaces is good practice. You explicitly allow per deployment.Use a podsecurity policy to have container restrictions and protect the Node Keep kube at the latest version.
## 14. what does kube-proxy do?
kube-proxy does 2 things

* for every Service, open a random port on the node and proxy that port to the Service.
* install and maintain iptables rules which capture accesses to a virtual ip:port and redirect those to the port in (1)
The kube-proxy is a component that manages host sub-netting and makes services available to other components.Kubeproxy handles network communication and shutting down master does not stop a node from serving the traffic and kubeproxy works, in the same way, using a service. The iptables will route the connection to kubeproxy, which will then proxy to one of the pods in the service.kube-proxy translate the destination address to whatever is in the endpoints.

## 15. What runs inside the kubernetes worker nodes?
Container Runtime

* Kubelet
* kube-proxy
Kubernetes Worker node is a machine where workloads get deployed. The workloads are in the form of containerised applications and because of that, every node in the cluster must run the container run time such as docker in order to run those workloads. You can have multiple masters mapped to multiple worker nodes or a single master having a single worker node. Also, the worker nodes are not gossiping or doing leader election or anything that would lead to odd-quantities. The role of the container run time is to start and managed containers. The kubelet is responsible for running the state of each node and it receives commands and works to do from the master. It also does the health check of the nodes and make sure they are healthy. Kubelet is also responsible for metric collectins of pods as well. The kube-proxy is a component that manages host subnetting and makes services available to other components.

## 16. Is there a way to make a pod to automatically come up when the host restarts?
Yes using replication controller but it may reschedule to another host if you have multiple nodes in the cluster

A replication controller is a supervisor for long-running pods. An RC will launch a specified number of pods called replicas and makes sure that they keep running. Replication Controller only supports the simple map-style `label: value` selectors. Also, Replication Controller and ReplicaSet aren't very different. You could think of ReplicaSet as Replication Controller. The only thing that is different today is the selector format. If pods are managed by a replication controller or replication set you can kill the pods and they'll be restarted automatically. The yaml definition is as given below:

```sh
apiVersion: v1
kind: ReplicationController
metadata:
name: test
spec:
replicas: 3
selector:
app: test
template:
metadata:
name: test
labels:
app: test
spec:
containers:
name: test
image: image/test
ports:
containerPort: 80
```
## 17. Is there any other way to update configmap for deployment without pod restarts?
well you need to have some way of triggering the reload. ether do a check every minute or have a reload endpoint for an api or project the configmap as a volume, could use inotify to be aware of the change. Depends on how the configmap is consumed by the container. If env vars, then no. If a volumeMount, then the file is updated in the container ready to be consumed by the service but it needs to reload the file

The container does not restart. if the configmap is mounted as a volume it is updated dynamically. if it is an environment variable it stays as the old value until the container is restarted.volume mount the configmap into the pod, the projected file is updated periodically. NOT realtime. then have the app recognise if the config on disk has changed and reload

## 18. Do rolling updates declared with a deployment take effect if I manually delete pods of the replica set with kubectl delete pods or with the dashboard? Will the minimum required a number of pods be maintained?

Yes, the scheduler will make sure (as long as you have the correct resources) that the number of desired pods are met. If you delete a pod, it will recreate it. Also deleting a service won't delete the Replica set. if you remove Service or deployment you want to remove all resources which Service created. Also having a single replica for a deployment is usually not recommended because you cannot scale out and are treating in a specific way
```sh
Any app should be `Ingress` -> `Service` -> `Deployment` -> (volume mount or 3rd-party cloud storage)

You can skip ingress and just have `LoadBalancer (service)` -> `Deployment` (or Pod but they don't auto restart, deployments do)
```
## 19. what is the difference between externalIP and loadBalancerIP ?
loadBalancerIP is not a core Kubernetes concept, you need to have a cloud provider or controller like metallb set up the loadbalancer IP. When MetalLB sees a Service of type=LoadBalancer with a ClusterIP created, MetalLB allocates an IP from its pool and assigns it as that Service's External LoadBalanced IP.the externalIP, on the other hand, is set up by kubelet so that any traffic that is sent to any node with that externalIP as the final destination will get routed.`ExternalIP` assumes you already have control over said IP and that you have correctly arranged for traffic to that IP to eventually land at one or more of your cluster nodes and its is a tool for implementing your own load-balancing. Also you shouldn't use it on cloud platforms like GKE, you want to set `spec.loadBalancerIP` to the IP you preallocated. When you try to create the service using .`loadBalancerIP` instead of `externalIP`, it doesn't create the ephemeral port and the external IP address goes to `<pending>` and never updates.

## 20. In  Kubernetes - A Pod is running 2 containers, when One container stops - another Container is still running, on this event, I want to terminate this Pod?
ou need to add a liveness and readiness probe to query each container,  if the probe fails, the entire pod will be restarted .add liveness object that calls any api that returns 200 to you from another container and both liveness and readiness probes run in infinite loops for example, If X depended to Y So add liveness  in X that check the health of Y.Both readiness/liveness probes always have to run after the container has been started .kubelet component performs the liveness/readiness checks and set initialDelaySeconds and it can be anything from a few seconds to a few minutes depending on app start time. Below is the configuration spec
```sh
livenessProbe spec:
livenessProbe:
httpGet:
path: /path/test/
port: 10000
initialDelaySeconds: 30
timeoutSeconds: 5
readinessProbe spec:
readinessProbe:
httpGet:
path: /path/test/
port: 10000
initialDelaySeconds: 30
timeoutSeconds: 5
```
## 21. what is the ingress, is it something that runs as a pod or on a pod?
An ingress is an object that holds a set of rules for an ingress controller, which is essentially a reverse proxy and is used to (in the case of nginx-ingress for example) render a configuration file. It allows access to your Kubernetes services from outside the Kubernetes cluster. It holds a set of rules. An Ingress Controller is a controller. Typically deployed as a Kubernetes Deployment. That deployment runs a reverse proxy, the ingress part, and a reconciler, the controller part. the reconciler configures the reverse proxy according to the rules in the ingress object. Ingress controllers watch the k8s api and update their config on changes. The rules help to pass to a controller that is listening for them. You can deploy a bunch of ingress rules, but nothing will happen unless you have a controller that can process them.
```sh
LoadBalancer service -> Ingress controller pods -> App service (via ingress) -> App pods
```
## 22. What happens if  daemonset can be set to listen on a specific interface since the Anycast IP will be assigned to a network interface alias
Yes, hostnetwork for the daemonset gets you to the host, so an interface with an Anycast IP should work. You'll have to proxy the data through the daemonset.Daemonset allows you to run the pod on the host network, so anycast is possible.Daemonset allows us to run the pod on the host network At the risk of being pedantic, any pod can be specified to run on the host network.  The only thing special about DaemonSet is you get one pod per host. Most of the issues with respect to IP space is solved by daemonsets. As kube-proxy is run as daemonset, the node has to be Ready for the kube-proxy daemonset to be up.

## 23.How to forward port `8080 (container) -> 8080 (service) -> 8080 (ingress) -> 80 (browser)` how is it done?
The ingress is exposing port 80 externally for the browser to access, and connecting to a service that listens on 8080. The ingress will listen on port 80 by default. An "ingress controller" is a pod that receives external traffic and handles the ingress  and is configured by an ingress resource For this you need to configure ingress selector and if no 'ingress controller selector' is specified then no ingress controller will control the ingress.

simple ingress Config will look like
```sh
host: abc.org
http:
paths:
backend:
serviceName: abc-service
servicePort: 8080
Then the service will look like
kind: Service
apiVersion: v1
metadata:
name: abc-service
spec:
ports:
protocol: TCP
port: 8080 # this is the port the service listens on
targetPort: 8080
```
## 24.Are deployments with more than one replica automatically doing rolling updates when a new deployment config is applied?
The Deployment updates Pods in a rolling update fashion when .spec.strategy.type==RollingUpdate .You can specify maxUnavailable and maxSurge to control the rolling update process. Rolling update is the default deployment strategy.kubectl rolling-update updates Pods and ReplicationControllers in a similar fashion. But, Deployments are recommended, since they are declarative, and have additional features, such as rolling back to any previous revision even after the rolling update is done.So for rolling updates to work as one may expect, a readiness probe is essential. Redeploying deployments is easy but rolling updates will do it nicely for me without any downtime. The way to make a  rolling update of a Deployment and kubctl apply on it is as below
```sh
spec:
minReadySeconds: 180
replicas: 9
revisionHistoryLimit: 20
selector:
matchLabels:
deployment: standard
name: standard-pod
strategy:
rollingUpdate:
maxSurge: 1
maxUnavailable: 1
type: RollingUpdate
```
## 25.If you have multiple containers in a Deployment file, does use the HorizontalPodAutoscaler scale all of the containers?

Yes, it would scale all of them, internally the deployment creates a replica set (which does the scaling), and then a set number of pods are made by that replica set. the pod is what actually holds both of those containers. and if you want to scale them independently they should be separate pods (and therefore replica sets, deployments, etc).so for hpa to work You need to specify min and max replicas  and the threshold what percentage of cpu and memory you want your pods to autoscale..without having the manually run kubectl autoscale deployment ,you can use the below yaml file to do the same 
```sh
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
annotations:
name: app
spec:
maxReplicas: 15
minReplicas: 10
scaleTargetRef:
apiVersion: autoscaling/v1
kind: Deployment
name: app targetCPUUtilizationPercentage: 70
```
## 26. Suppose you have to use database with your application but well, if you make a database container-based deployment. how would the data persist?

Deployments are for stateless services, you want to use a StatefulSet or just define 3+ pods without a replication controller at all. If you care about stable pod names and volumes, you should go for StatefulSet.Using statefulsets you can maintain which pod is attached to which disk.StatefulSets make vanilla k8s capable of keeping Pod state (things like IPs, etc) which makes it easy to run clustered databases. A stateful set is a controller that orchestrates pods for the desired state. StatefulSets formerly known as PetSets will help for the database if hosting your own. Essentially StatefulSet is for dealing with applications that inherently don't care about what node they run on, but need unique storage/state

## 27. If a pod exceeds its memory "limit" what signal is sent to the process?
SIGKILL as immediately terminates the container and spawns a new one with OOM error. The OS, if using a cgroup based containerisation (docker, rkt, etc), will do the OOM killing. Kubernetes simply sets the cgroup limits but is not ultimately responsible for killing the processes.`SIGTERM` is sent to PID 1 and k8s waits for (default of 30 seconds) `terminationGracePeriodSeconds` before sending the `SIGKILL` or you can change that time with terminationGracePeriodSeconds in the pod. As long as your container will eventually exit, it should be fine to have a long grace period. If you want a graceful restart it would have to do it inside the pod. If you don't want it killed, then you shouldn't set a memory `limit` on the pod and there's not a way to disable it for the whole node. Also, when the liveness probe fails, the container will SIGTERM and SIGKILL after some grace period.