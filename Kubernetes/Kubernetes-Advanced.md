### 1.Let’s say a Kubernetes job should finish in 40 seconds, however on a rare occasion it takes 5 minutes, How can I make sure to stop the application if it exceeds more than 40 seconds?
When we create a job spec, we can give --activeDeadlineSeconds flag to the command, this flag relates to the duration of the job, once the job reaches the threshold specified by the flag, the job will be terminated.
```sh
kind: CronJob
apiVersion: batch/v1beta1
metadata:
  name: mycronjob
spec:
  schedule: "*/1 * * * *"
activeDeadlineSeconds: 200
  jobTemplate:
    metadata:
      name: google-check-job
    spec:
      template:
        metadata:
          name: mypod
        spec:
          restartPolicy: OnFailure
          containers:
            - name: mycontainer
             image: alpine
             command: ["/bin/sh"]
             args: ["-c", "ping -w 1 google.com"]
```             
### 2.How do you test a manifest without actually executing it?
use --dry-run flag to test the manifest. This is really useful not only to ensure if the yaml syntax is right for a particular Kubernetes object but also to ensure that a spec has required key-value pairs.

kubectl create -f <test.yaml> --dry-run

Let us now look at an example Pod spec that will launch an nginx pod
```sh
○ → cat example_pod.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx
  namespace: mynamespace
spec:
  containers:
    - name: my-nginx
      image: nginx
○ → kubectl create -f example_pod.yaml --dry-run
pod/my-nginx created (dry run)
```
### 3.How do you initiate a rollback for an application?
Rollback and rolling updates are a feature of Deployment object in the Kubernetes. We do the Rollback to an earlier Deployment revision if the current state of the Deployment is not stable due to the application code or the configuration. Each rollback updates the revision of the Deployment
```sh
○ → kubectl get deploy
NAME    DESIRED  CURRENT UP-TO-DATE   AVAILABLE AGE
nginx   1  1 1            1 15h
○ → kubectl rollout history deploy nginx
deployment.extensions/nginx
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
kubectl undo deploy <deploymentname>
○ → kubectl rollout undo deploy nginx
deployment.extensions/nginx
○ → kubectl rollout history deploy nginx
deployment.extensions/nginx
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
We can also check the history of the changes by the below command

kubectl rollout history deploy <deploymentname>
```
### 4.How do you package Kubernetes applications?

Helm is a package manager which allows users to package, configure, and deploy applications and services to the Kubernetes cluster.

helm init  # when you execute this command client is going to create a deployment in the cluster and that deployment will install the tiller, the server side of Helm

The packages we install through client are called charts. They are bundles of templatized manifests. All the templating work is done by the Tiller
```sh
helm search redis # searches for a specific application
helm install stable/redis # installs the application
helm ls # list the applications 
```
### 5.What are init containers?
Generally, in Kubenetes, a pod can have many containers. Init container gets executed before any other containers run in the pod.
```sh
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
  annotations:
    pod.beta.Kubernetes.io/init-containers: '[
        {
            "name": "init-myservice",
            "image": "busybox",
            "command": ["sh", "-c", "until nslookup myservice; do echo waiting for myservice; sleep 2; done;"]
        }
    ]'
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
```    

### 6.What is node affinity and pod affinity?
* Node Affinity ensures that pods are hosted on particular nodes.
* Pod Affinity ensures two pods to be co-located in a single node.

* Node Affinity
```sh
apiVersion: v1
kind: Pod
metadata:
name: with-node-affinity
spec:
affinity:
nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: Kubernetes.io/e2e-az-name
            operator: In
            values:
            - e2e-az1
```            
* Pod Affinity
```sh
apiVersion: v1
kind: Pod
metadata:
name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
```
The pod affinity rule says that the pod can be scheduled to a node only if that node is in the same zone as at least one already-running pod that has a label with key “security” and value “S1”

### 7.How do you drain the traffic from a Pod during maintenance?
When we take the node for maintenance, pods inside the nodes also take a hit. However, we can avoid it by using the below command

`kubectl drain <nodename>`
When we run the above command it marks the node unschedulable for newer pods then the existing pods are evicted if the API Server supports eviction else it deletes the pods

Once the node is up and running and you want to add it in rotation we can run the below command

`kubectl uncordon <nodename>`
Note: If you prefer not to use kubectl drain (such as to avoid calling to an external command, or to get finer control over the pod eviction process), you can also programmatically cause evictions using the eviction API.

### 8. I have one POD and inside 2 containers are running one is Nginx and another one is  wordpress So, how can access these 2 containers from the Browser with IP address?
Just do port forward `kubectl port-forward [nginx-pod-name] 80:80 kubectl port-forward [wordpress-pod-name] drupal-port:wordpress-port`.

To make it permanent, you need to expose those through nodeports whenever you do kubectl port forward it adds a rule to the firewall to allow that traffic across nodes but by default that isn’t allowed since flannel or firewall probably blocks it.proxy tries to connect over the network of the apiserver host as you correctly found, port-forward on the other hand is a mechanism that the node kubelet exposes over its own API

### 9.If I have multiple containers running inside a pod, and I want to wait for a specific container to start before starting another one.
One way is  Init Containers are for one-shot tasks that start, run, end; all before the next init container or the main container start, but  if a client in one container wants to consume some resources exposed by some server provided by another container or If the server  ever crashes or is restarted, the client will need to retry connections. So the client can retry always, even if the server isn't up yet. The best way is sidecar pattern_ are where one container is the Main one, and other containers expose metrics or logs or encrypted tunnel or somesuch. In these cases, the other containers can be killed when the Main one is done/crashed/evicted.

### 10.What is the impact of upgrading kubelet if we leave the pods on the worker node - will it break running pods? why?
Restarting kubelet, which has to happen for an upgrade will cause all the Pods on the node to stop and be started again. It’s generally better to drain a node because that way Pods can be gracefully migrated, and things like Disruption Budgets can be honored. The problem is that `kubectl` keeps up with the state of all running pods, so when it goes away the containers don’t necessarily die, but as soon as it comes back up, they are all killed so `kubectl` can create a clean slate. As kubelet communicates with the apiserver, so if something happens in between of upgrade process, rescheduling of pods may take place and health checks may fail in between the process. During the restart, the kubelet will stop querying the API, so it won’t start/stop containers, and Heapster won’t be able to fetch system metrics from cAdvisor. Just make sure it’s not down for too long or the node will be removed from the cluster!

### 11. How service that selects apps based on the label and has an externalIP?
The service selects apps based on labels, so if no pods have appropriate labels, the service has nothing to route and labels can be anything you like. Since all pod names should be unique, you can just set the labels as the pod name. Since statesets create the same pods multiple times, they won't be configured with distinct labels you could use to point disparate services to the correct pod. If you gave the pods their own labels manually it will work. Also, service selects pods based on selector as well their location label as well Below .yaml file of Grafana dashboard service shows the same
```sh
apiVersion: v1
kind: Service
metadata:

name: grafanaportforward
namespace: kubeflow
labels:

run: grafana-test
spec:

ports:

- port: 3000
protocol: TCP
name: grafana
externalIPs:- x.y.x.q
selector:app: grafana-test
```
### 12.Does the container restart When applying/updating the secret object (kubectl apply -f mysecret.yml)?  If not, how is the new password applied to the database?
If you are mounting the secret as a volume into your pod, when the secret is updated the content will be updated in your pod, without the pod restarting. It's up to your application to detect that change and reload, or to write your own logic that rolls the pods if the secret changes .volumeMount controls what part of the secret volume is mounted into a particular container (defaults to the root, containing all those files, but can point to a specific file using `subPath`), and where in the container it should be mounted with `mountPath`.Example spec is below
```sh
volumeMounts:
- readOnly: true
mountPath: /certs/server
name: my-new-server-cert
volumes:
- name: server-cert
secret:
secretName: mysecret
```
Also, it depends on how the secret is consumed by a container. If env vars, then no. If a volumeMount, then the file is updated in the container ready to be consumed by the service but it needs to reload the file. The container does not restart. if the secret is mounted as a volume it is updated dynamically. if it is an environment variable it stays as the old value until the container is restarted

### 13.How should you connect an app pod with a database pod?
By using a service object. reason being, if the database pod goes away, it's going to come up with a different name and IP address. Which means the connection string would need to be updated every time, managing that is difficult. The service proxies traffic to pods and it also helps in load balancing of traffic if you have multiple pods to talk to. It has its own IP and as long as service exists pod referencing this service in upstream will work and if the pods behind the service are not running, a pod will not see that and will try to forward the traffic but it will return a 502 bad gateway.So just defined the Service and then bring up your Pods with the proper label so the Service will pick them up.

### 14.How to configure a default ImagePullSecret for any deployment?
You can attach an image pull secret to a service account. Any pod using that service account (including default) can take advantage of the secret.you can bind the pullSecret to your pod, but you’re still left with having to create the secret every time you make a namespace.

* imagePullSecrets:
* name: test
Also, you can  Create the rc/deployment manually and either specify the imagepullsecret or a service account that has the secret or add the imagepullsecret to the default service account, in which case you'd be able to use `kubectl run` and not have to make any manual changes to the manifest. Depending on your environment and how secret this imagepullsecret is, will change how you approach it.

### 15. I have a configmap for 3 files that are going to be mounted in supposing "fluentd/etc/" and the respective files would be fluent.conf,  kubernetes.conf, systemd.conf, config map in deployment.yaml is like this
```sh
volumeMounts:
name: fluentd
mountPath: /fluentd/etc
name: varlog
mountPath: /var/log
name: container1
mountPath: /var/lib/docker/containers
readOnly: true
securityContext:
privileged: true
terminationGracePeriodSeconds: 30
volumes:
name: varlog
hostPath:
path: /var/log
name: container1
hostPath:
path: /var/lib/docker/containers
name:  fluentd
configMap:
name: fluentd-config
```
When deploying you will get an error of mounting as read-only, which is effecting to fluent to read some of the mentioned sources in the configmap.how can we avoid this read-only error?

```sh
configmaps are always mounted read-only. if you need to modify a configmap in a pod, you should copy it from the configmap mount to a regular file in the pod and then modify it. To solve this issue we should use an init container to mount the configmap, copy the configmap into an `emptyDir` volume and share the volume with the main container.

configmaps are mounted read-only so that you can't touch the files. when the master configmap changes the mounted file also changes. so if you were to modify the local mounted file, it would be overwritten anyways.
```

### 16. If you have a pod that is using a ConfigMap which you updated, and you want the container to be updated with those changes, what should you do?
if the config map is mounted into the pod as a volume, it will automatically update not instantly and the files will change inside the container. If it is an environment variable it stays as the old value until the container is restarted

* For example: create a new config.yaml with your custom values
```sh
apiVersion: v1
kind: ConfigMap
metadata:
name: testconfig
namespace: default
data:
config.yaml: |
namespaces:
default
labels:
"app"
"owner"
```
* Then create a pod definition, referencing the ConfigMap
```sh
apiVersion: v1
kind: Pod
metadata:
name: testobject
spec:
serviceAccountName: testobject
containers:
name: testobject
image: test/appv1
volumeMounts:
name: config-volume
mountPath: /app/config.yaml
subPath: config.yaml
volumes:
name: config-volume
configMap:
name: testconfig
restartPolicy: Never
```