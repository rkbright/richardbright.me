---
layout:     post
title:      Certified Kubernetes Administrator (CKA) Study Guide 
date:       2022-06-01
summary:    CKA Study Notes
permalink: /cka-study-guide/
toc: true
tags:
  - cka
---

![cka](https://richardbright.me/images/cka_logo.png)


Helpful prerequisite skills:

- Docker
- YAML

Official Site:

- [kubernetes](https://kubernetes.io/){:target="_blank"}

# Certification Objectives

- [Core concepts](#lesson1) 
  
  - Cluster architecture 
  - API primitives
  - Services and other network primitives 

- [Scheduling](#lesson2) 

  - Labels and selectors 
  - Daemon sets
  - Resource limits 
  - Multiple schedulers 
  - Manual scheduling 
  - Scheduler events
  - Configure kubernetes scheduler 

- [Logging and maintenance](#lesson3) 

  - Monitor cluster components 
  - Monitor applications 
  - Monitor cluster component logs 
  - Application logs

- [Application lifecycle manager](#lesson4)

  - Rolling updates and rollbacks in deployments 
  - Configure applications 
  - Scale applications 
  - Self-healing application 

- [Cluster maintenance](#lesson5) 

  - Cluster upgrade process
  - Operating system upgrades 
  - backup and restore methodologies 

- [Security](#lesson6) 

  - Authentication and authorization 
  - TLS certificates for cluster components 
  - Kubernetes security 
  - Images securely 
  - Network policies 
  - Security context 
  - Secure persistent key value store 

- [Storage](#lesson7) 

  - Persistent volumes 
  - Persistent volume claims 
  - Configure applications with persistent storage 
  - Access modes for volumes 
  - Kubernetes storage object 

- [Networking](#lesson8)  

  - Network configuration and cluster nodes
  - Service network 
  - POD network concepts 
  - Network loadbalancer 
  - Ingress
  - Cluster DNS
  - CNI

- Installation, configuration and validation 

  - Design a kubernetes cluster
  - Install kubernetes master and nodes 
  - Secure cluster communications 
  - HA kubernetes cluster 
  - Provision infrastructure 

- Troubleshooting 

  - Application failure 
  - Worker node failure 
  - Control plane failure 
  - Networking 


Helpful exam links:

[Certified Kubernetes Administrator](https://www.cncf.io/certification/cka/)

[Exam Curriculum (Topics)](https://github.com/cncf/curriculum)

[Candidate Handbook](https://www.cncf.io/certification/candidate-handbook)

[Exam Tips](http://training.linuxfoundation.org/go//Important-Tips-CKA-CKAD)

[CertificationTips](#certificationtips)

## Lesson1

### Core Concepts Section Introduction

**Master nodes** manage, plan, schedule and monitor. The master node is also referred to as the **control plane**. 
  - **etcd** is a database that stores important data about the environment in a key value format 
  - **kube-scheduler** manages images, container deployments, policies etc.. within the cluster 
  - **node-controller** is responsible for onboarding new nodes to the cluster 
  - **replication-controller** ensures the desired number of containers are running at all times within a specific replication group 
  - **kube-apiserver** is responsible for orchestrating all operations within the cluster; exposes the kubernetes api and is the primary interface for interacting with the cluster 

**Worker nodes** host applications as containers 
  - **container runtime engine** is needed to run the application components, e.g., Docker. 
  - **kubelet** is an engine that runs on each node in the cluster and listens for instructions via the kube-apiserver 
  - **kube-proxy** runs on the worker nodes and ensures the rules are in place so containers can communicate 



### ETCD for Beginners

**etcd** is a distributed and reliable key value store that is simple, secure and fast 
  - **key value stores** are mainly used to store configuration data that can be saved and retrieved quickly compared to tabular data 
  - listens on port **2379** by default 
  - **etcd control client** is the default client; it is the CLI for etcd 
  - `./etcdctl set key1 value1` will add an entry to the database 
  - `./etcdctl get key1` will retrieve the data 
  - `./etcdctl` to see help page(s) 

### ETCD in Kubernetes

**etcd** stores data on the following components 
  - Nodes
  - PODs
  - Configurations
  - Secrets 
  - Accounts
  - Roles 
  - Bindings
  - Other... 

All data retrieved from the `kubectl get` command comes from the `etcd` server 

All changes made to the cluster are stored in the `etcd` server, e.g., deploying PODs, Replicasets, and adding Nodes

Applied changes are not considered complete until the change is made in the `etcd` server 

There are two methods for deploying `etcd` within a kubernetes cluster: 

1) Manual install 

   - required to download, install, and configure `etcd` yourself as a service 
   - will need to generate TLS certificates 

2) Using `kubeadm`

  - will automatically deploy the `etcd` service via a POD in the kube-system namespace 
  - `./etcd get / --prefix -keys-only` to retrieve all keys stored by kubernetes 

### Kube API Server 

 Is the primary management component in kubernetes 

`kubectl` commands go to the `kube-apiserver` where the request is authenticated and validated 

The `kube-apiserver` will then query `etcd` for the information and respond back to the request 

`kube-apiserver` is the only component that interacts directly with the `etcd` datastore 

`kube-apiserver` is responsible for: 

  - authenticating users
  - validating requests 
  - retrieving data 
  - updating `etcd`
  - services that depend on `kube-apiserver`
    - `scheduler` 
    - `kubelet` 

If using `kubeadm` tool to bootsrap cluster, then you do not need to install the `kube-apiserver` manually. If setting up the cluster manually, then you can install the `kube-apiserver` from the kubernetes release page. 

`kubeadm` will deploy the `kube-apiserver` as a pod in the kube system namespace on the master node

`kubectl get pods -n kube-system` 

You can view options at `cat /etc/kubernetes/manifests/kube-apiserver.yaml` 

In a non-kubeadm setup, you can view options in `cat /etc/systemd/system/kube-apiserver.service` 

You can also see the running process and options by running `ps -aux | greg kube-apiserver` 

### Kube Controller Manager

A controller is a process that continuously monitors the state of various components in the system and works toward bringing the whole system to the desired functioning state. 

`Node-Controller` is responsible for monitoring the nodes and taking necessary action to keep the application running. 

  - it does this via the `kube-apiserver` 
  - the `Node-Controller` checks the status of the nodes every 5 seconds; it monitors the health of the nodes 
  - if the `Node-Controller` stops receiving a signal from a node, then the `Node-Controller` marks the node as unreachable after 40 seconds  
  - after a node is marked unreachable, the `Node-Controller` waits 5 minutes for the node to come back up. If the node does not come online then the pod is removed from the node and provisions the pod on the healthy nodes if it's part of a replicaset

`Replication-Controller ` is responsible for monitoring the status of the replicationset and ensuring the desired number of pods are available at all times within the set. If a pod dies a new one will be created

Controllers are the brains behind kubernetes: 

- Deployment-Controller 
- Namespace-Controller 
- Endpoint-Controller 
- CronJob
- Job-Controller 
- PV-Protection-Controller 
- Service-Account-Controller 
- Stateful-Set 
- Replicaset
- Node-Controller
- PV-Binder-Controller 
- Replication-Controller 

All of these controllers are packaged into a single process known as the `Kube-Controller-Manager` 

You can download the `kube-controller-manager`, install it and run it as a service 

All controllers are enabled by default, you can elect to list controllers as well 

`kubeadm` install, you can list options by running `cat /etc/kubernetes/manifests/kube-controller-manager.yaml` 

In a non `kubeadm` install, you can list options by running `cat /etc/systemd/system/kube-controller-manager.service`

View process `ps -aux | greg kube-controller-manager`

### Kube Scheduler

Responsible for deciding which pods goes on which nodes, it does not actually deploy the pod on the node (that is the job of the kubelet)

The scheduler bases it's decision on a few criteria: 

- Figures out placement in two phases 

    - The first phase will try filtering out the nodes that do not meet the pod specification, for example, a 10 CPU pod will need to be placed on a machine with enough CPU capacity. Other lower spec nodes will be filtered out 

    - The second phase is based on ranking the nodes. Each node will be given a score between 1-10, the scheduler will assign a pod based on the remaining specs of the node once the pod is placed on the node. 

You can also write your own customized scheduler 

You can install the scheduler by downloading the binary, installing it, and running it as a service. 

`kubeadm` install, you can list options by running `cat /etc/kubernetes/manifests/kube-scheduler.yaml` 

View process `ps -aux | greg kube-scheduler`

### Kubelet

the `kubelet` is the master of the node, it is responsible for all tasks on the pod and frequently sends data back to the master node on the status of the environment and the containers on them 

Responsibilities include:
  - registers node with the kubernetes cluster
  - initiate container runtime to pull docker image to crete pod
  - Monitors the state of the pod and the container and reports back to the master node 

**NOTE** `kubeadm` does not deploy `kubelet`, you must always install `kubelet` manually on the worker nodes 

You can download the file from the kubernetes site. Install it of the worker node and run it as a service 

View process `ps -aux | greg kubelet`

### Kube Proxy

In a cluster, all pods are able to communicate with each other, this is accomplished by deploying a pod network solution 

`kube-proxy` runs on each node in the kubernetes cluster 

`kube-proxy` leverages linux `iptables` 

`kubeadm` will install `kube-proxy` on each node as a pod

`kube-proxy` is deployed as a deamonset, so each node in the cluster will have a dedicated `kube-proxy` pod 

You can also download and install `kube-proxy` from the kubernetes homepage 

### PODs

When working with `pods`, there are a few assumptions: 

1) the application is available as a docker image in a docker registry 

2) a kubernetes cluster has already been set up 

Applications are encapsulated by a kubernetes object that is known as a `pod`

`pods` are the smallest object you can create in kubernetes 

`pods` can be deployed and scaled on existing and/or new nodes in the cluster 

`pods` usually have a one-to-one ratio with the containers running the application 

`pods` are not deployed on existing `pods` to scale the application 

`pods` can run multiple containers but not usually of the same kind. For example, helper containers are usually deployed on pods

running multi-pod containers is a rare use case, typically it should be one pod per docker container 

Helpful commands for managing `pods`:

`kubectl get pods` to generate list of pods 

  * use the `-o wide` option to print additional details 

`kubectl run nginx --image=nginx` to create a pod running an nginx container

`kubectl describe pod nginx` to printout nginx pod information 

`kubectl create -f nginx.yaml` to create the nginx pod from a yaml file 

`kubectl apply -f nginx.yaml` to apply changes to an existing pod 

`kubectl edit pod nginx` can also be used to make edits to a pod's configuration

`kubectl delete pod nginx` to delete a pod 

`kubectl run nginx --image=nginx --dry-run=client -o yaml` to print out yaml specification that you can use to configure the nginx pod

standard pod yaml configuration 

```
apiVersion: v1
kind: Pod
metadata: 
  name: nginx
  labels: 
    app: nginx-webapp
spec:
  containers:
    - image: nginx:alpine
      name: nginx
```
Examples:

`kubectl run redis --image=redis:alpine --labels="tier=db"`

`kubectl run custom-nginx --image=nginx --port=8080`

### ReplicaSets

the `replication-controller` is responsible for running multiple instances of a pod in the kubernetes cluster and thus providing high availability 

the `replication-controller` can be deployed on a multi node cluster or on a single node. 

`replication-controller` provides load balancing and scalability by deploying more pods within the same node or on additional nodes 

kubernetes has two common terms used for replication 

| `replication-controller` | `replicaset` | 
|---|---|
| older technology that is being replaced by `replicaset`  | is the new recommended way to setup replication  | 

Example `replication-controller` definition file `rc-replication.yaml`

```
apiVersion: v1
kind: ReplicationController 
metadata: --> metadata section for the replication controller 
  name: myapp
  labels: 
    app: myapp
    type: front-end 
spec: --> spec section for the replication controller 
  template:

    metadata: --> metadata section for the pod
      name: myadd-pod
      labels: 
        app: myapp
        type: front-end
    spec: --> spec section for the pod
      containers:
      - name: nginx-container
        image: nginx  

replicas: 3
```

run `kubectl create -f rc-replication.yaml` to create 3 replication controller pods of the same type

`kubectl get replicationcontroller` to print out replication controller data within the cluster 

Example `replicaset` definition file `replicaset-definition.yaml`

```
apiVersion: apps/v1
kind: ReplicaSet 
metadata: --> metadata section for the replicaset 
  name: myapp-replicaset
  labels: 
    app: myapp
    type: front-end 
spec: --> spec section for the replicaset
  template:

    metadata: --> metadata section for the pod
      name: myadd-pod
      labels: 
        app: myapp
        type: front-end
    spec: --> spec section for the pod
      containers:
      - name: nginx-container
        image: nginx  

replicas: 3
selector:   --> additional required setting
  matchLabels:
    type: front-end 
```

`replicasets` can be used to manage pods that were not created as part of the initial `replicaset` 

`rs` is the short form for `replicaset`

run `kubectl create -f replicaset-definition.yaml` to create 3 replication controller pods of the same type

`kubectl get replicaset` to print out replicaset controller data within the cluster 

Why do we label pods in kubernetes? 

Labels allow for the replicaset controller to monitor only those pods with the same label name 

The templates definition section is required in order for a `replicaset` to maintain a desired number of pods 

How do you scale the `replicaset`?

Given the example configuration above, replace the `replicas` value from 3 to 6 to expand the pods 

Run `kubectl replace -f replicaset-definition.yaml` to update the replicaset` 

You can also use the `kubectl scale` command to apply the update 

`kubectl scale replicas=6 -f replicaset-definition.yaml` to update the replicaset to 6 and update the definition file 

`kubectl scale replicas=6 replicaset myapp-replicaset` will scale the replicaset to 6 but will not persist the change to the definitions file 

Useful commands:

`kubectl create -f replicaset-definition.yaml`

`kubectl get replicaset` 

`kubectl delete replicaset myapp-replicaset` will also delete pods 

`kubectl replace -f replicaset-definition.yaml` to update the `replicaset` and definitions file 

`kubectl scale --replicas=6 -f replicaset-definition.yaml` or `kubectl scale --replicas=6 replicaset myapp-replicaset` to scale the cluster 

`kubectl describe replicaset myapp-replicaset` to see `replicaset` data 

`kubectl explain replicaset` to print an explication of the replicaset definition yaml file

### Deployments

`deployments` are a kubernetes object that is higher in the kubernetes hierarchy

`deployments` allow you to perform: 

- rolling updates 
- upgrades to container instances 
- roll back and undo changes if needed
- pause updates/upgrades 

`deployment-definition.yaml`
```
apiVersion: apps/v1
kind: Deployment 
metadata: --> metadata section for the deployment 
  name: myapp-replicaset
  labels: 
    app: myapp
    type: front-end 
spec: --> spec section for the deployment
  template:

    metadata: --> metadata section for the pod
      name: myadd-pod
      labels: 
        app: myapp
        type: front-end
    spec: --> spec section for the pod
      containers:
      - name: nginx-container
        image: nginx  

replicas: 3
selector:   --> additional required setting
  matchLabels:
    type: front-end 
```

`kubectl create -f deployment-definition.yaml` to create deployment 

`kubectl get deployment` to view the deployment data 

a `deployment` will automatically create a `replicaset`

`kubectl get replicas` to view replica data 

`kubectl get pods` to view `deployment` `pods`

`kubectl get all` to view all cluster data 

Examples: 

`kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3`

`kubectl create deployment redis-deploy --image=redis --namespace=dev-ns --replicas=2`

### Useful Certification Tips

Create an NGINX Pod

`kubectl run nginx --image=nginx`

Generate POD Manifest YAML file (-o yaml). Don’t create it(–dry-run)

`kubectl run nginx --image=nginx --dry-run=client -o yaml`

Create a deployment

`kubectl create deployment --image=nginx nginx`

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) with 4 Replicas (–replicas=4)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.

`kubectl create -f nginx-deployment.yaml`

OR

In k8s version 1.19+, we can specify the –replicas option to create a deployment with 4 replicas.

`kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml`

### Services

kubernetes services enable communication among various components within and outside of the application 

It helps connect applications to each other and/or to users

Services enable connectivity among groups of pods 

Services enables loose coupling among application microservices 

`svc` is short for service when using `kubectl` 

`Node-port service` listens to ports on the node and forwards requests to the mapped port on the pod running the application 

**Service Types**

- **Node-port**: forwards node port requests to the application container 
- **ClusterIP**: creates a virtual IP within the cluster to enable communication across services 
- **Loadbalancer**: provisions a loadbalancer for the application of supported CSPs. 

**Ports** 

**Target-port**: port on the pod, where the service forwards the request 

**Port**: port on the service, associated to the clusterIP 

**Node-port**: port on the node, used to access the application externally
  - has a valid range between 30000 - 32767

`service-definitions.yaml`
```
apiVersion: v1
kind: Service
metadata: 
  name: myapp-service
spec: 
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: myapp
    type: front-end

```

The only mandatory field is port, the target port will get the same number as port and the nodePort will get a port number in the valid range

the `ports` section in the definitions file is an array 

`labels` and `selectors` are used to link services with pods

Use the labels under the pod definition files that was used to create the pod, copy the information in the labels section 

In a production environment there could be multiple pods running the application, the service will recognize if one or more pods are available and assign all three pods as endpoints to forward requests from the user.

Services also acts as a loadbalancer to distribute load across the pods

The service will use a `random` algorithm when forwarding requests 

When an application is deployed on separate nodes on the cluster, kubernetes will create a service tht spans all the nodes in the cluster and will map the `targetPort` to all the `nodePorts` on the cluster 

Services creation is exactly the same no matter if you are working on a: 

- single pod on a single node
- multiple pods on a single node
- multiple pods on multiple nodes 

### Services Cluster IP

provides a single network interface for services that are deployed across various layers within a microservices architecture 

`service-definition.yaml`
```
apiVersion: v1
kind: Service
metadata:
  name: back-end
spec: 
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
  
  selector: 
    app: myapp
    type: back-end

```


### Services – Loadbalancer

Can manually configure a load  balancer service 

Can integrate with native CSP load balancer service

`service-definition.yaml`
```
apiVersion: v1
kind: Service
metadata:
  name: back-end
spec: 
  type: Loadbalancer
  ports:
    - targetPort: 80
      port: 80
      nodePort: 3008
```

Examples:

`kubectl expose pod redis --port=6379 --name redis-service`

`kubectl expose pod httpd --port=80 --name httpd`

### Namespaces

kubernetes creates three `namespaces` by default when creating a cluster

- `kube-system` internal kubernetes resources 
- `default` the default application namespace 
- `kube-public` resources that should be available to all the users 

`namespaces` provide isolation between resources 

each `namespace` can have it's own set of policies that defines who can do what 

resource quotas can be applied to each `namespace`

append the `namespace` and service to connect resources across  `namespaces`

for example, `mysql.connect("db-service.dev.svc.cluster.local")`

- service name `db-service`
- namespace `dev`
- service `svc`
- domain `cluster.local` 

`kubectl get pods` only lists pods in the current `namespace` 

to see services in another `namespace` use the `--namespace=` option

`kubectl get pods --namespace=kube-system`

when creating a pod, it is created in the default `namespace`

`kubectl create -f pod-definition.yml`

to create services in another `namespace` use the `--namespace=` option

`kubectl create -f pod-definition.tml --namespace=dev`

you can also add the `namespace` option to the definitions file to create automatically 

`pod-definitions.yml`
```
apiVersion: v1
kind: Namespace 
metadata:
  name: myadd-pod
  namespace: dev
  labels:
    app: myapp
    type: front-end
  spec: 
    containers:
      - name: nginx-container
        image: nginx
```

`kubectl create -f namespace-dev.yml`

or 

`kubectl create namespace dev`

you can set the `namespace` in the current context to dev

`kubectl config set-context $(kubectl config current-context) --namespace=dev`

view all pods in all `namespaces` with `kubectl get pods --all-namespaces` 

resource quota definition files are used

`compute-quota.yml`
```
apiVersion: va
kind: ResourceQuota
metadata:
  name: computer-quota
  namespace: dev

spec:
 hard: 
   pods: "10"
   request.cpu "4"
   request.memory: 5 Gi
   limits.cpu: "10"
   limits.memory=10Gi
```

### Imperative vs Declarative

| `imperative` | `declarative` | 
|---|---|
| specifying what to do and how to do it  | specifying what to do | 
| *`kubectl run --image=nginx nginx` |  `kubectl apply -f nginx.yaml`    |
|  *`kubectl create deployment --image=nginx nginx   `   | you can specify a directory file if there are multiple files `kubectl apply -f /path/to/folder`  |
| * `kubectl expose deployment nginx --port=80`| will update the object and runtime environment |
| **`kubectl edit deployment nginx`||
| **`kubectl scale deployment nginx --replicas=5`||
| **`kubectl set image deployment nginx nginx=nginx:1.18`||
| **`kubectl create -f nginx.yaml`||
| **`kubectl replace -f nginx.yaml`||
| **`kubectl delete -f nginx.yaml`||
|does not apply updates to the object files, you will need to ensure runtime changes are also made in the configuration file||

> `*` = create objects 

> `**` = update objects, difficult to work with in a production environment since nobody else knows what command you ran 

<!-- | **IaC Example**  |
| List of full step-by-step instructions | Declare environment specifications and let the software install | -->

### Kubectl Apply Command

the `kubectl apply` command takes into consideration the

- local configuration file 
- last applied configuration 

before making a decision on what changes to be made. 

If an object does not exist, the object will be created and a file will be created on the live kubernetes cluster that is also known as the live object configuration 

## Lesson2

### Scheduling 

How scheduling works.

- every definition file has a field called `nodeName` that is not set or specified by default, kubernetes adds it automatically 
- kubernetes will scan pods and look for pods where the `nodeName` is not set
- once identified, kubernetes will schedule the pod and bind the pod to the node by adding a value to the `nodeName` field
- pods will continue to be in a pending state if there is no schedule service on the node
- you can manually include the node information to the `nodeName` when creating the pod, you cannot update the `nodeName` field once the pod is created 
- to assign a pod to a node post creation, and where the pod is not being managed by the scheduler, you can create a binding object file and submit a POST API call to the binding api. The api call must be in json format

`pod-bind-definition.yaml`
```
apiVersion: v1
kind: Binding
metadata: 
  name: nginx
target:
  apiVersion: v1
  kind: Node
  name: Node02
```

`curl --header "Content-Type:application/json" --request POST --data '{"apiVersion":"v1","kind"."Binding",...} http://hostname/api/v1/namespaces/default/pods/$PODNAME/binding`


Examples

`kubectl get nodes` will return the node names in the cluster and its status

`kubectl get pods -n kube-system` to print the services running in the kube-system namespace 

`kubectl replace --force -f nginx.yaml` to delete and recreate a pod with the new specifications 

`kubectl get pods --watch` to monitor pod 


### Labels and Selectors

labels and selectors are a standard way to group things together 

labels are properties attached to each item 

selectors help you filter items 

labels are added through a definition file under the metadata section 

you can add as many labels as needed

`kubectl get pods --selector app=App1` to select the App1 pod

you can add an `annotations` section in the definition file to add general data 

`kubectl get pods --selector env=prod,bu=finance,tier=frontend`

`kubectl get pods --selector env=prod,bu=finance,tier=frontend --no-headers` will suppress the headers line when printing to stdout 

`kubectl label node node01 color=blue` add a label to existing node

### Taints and Tolerations

Are used to apply restrictions on what pods can be scheduled on a node 

Used to restrict nodes from accepting certain pods 

On a regular kubernetes deployment the scheduler will try to deploy the pods evenly across the available nodes 

When a taint is applied to a node, the scheduler cannot place a pod on the node because pods by default have no tolerations 

This results in no unwanted pods being places on a node 

To allow a pod to be placed on a tainted node, apply the toleration to the pod so it is tolerant of the taint on the node 

taints are set on nodes 

tolerations are set on pods 

`kubectl taint nodes node-name key=value:taint-effect`

Three options for taint-effect 

- `NoSchedule` will not schedule pods on the node 
- `PreferNoSchedule` kubernetes will try to avoid placing a pod on the node but it's not guaranteed
- `Noexecute` new pods will not be scheduled on the node and existing pods will be evicted if the tolerations are not applied to the pods. pods can still be scheduled on untainted nodes. to bind a pod to a node, that can be achieved through node affinity   

Example:

`kubectl taint nodes node01 app=blue:NoSchedule`

values in the pod definition file must be in double quotes `" "`

`pod-definition.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  name:myapp-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
tolerations:
- key:"app"
  operator:"Equal"
  value:"blue"
  effect:"NoSchedule"
```

the scheduler does not schedule any pods for on the master node, kubernetes applies a taint on the master node when created that prevents the scheduler from placing pods on the master node 

`kubectl get pods kubemaster | grep Taint` to print the taint on the master node

add a `-` to the end of a taint to remove from a node 

`kubectl taint node controlplane node-role.kuberneties.io/master:NoSchedule-`


### Node Selectors

used to assign pods with specific workloads to a desired node 

for example, a data processing pod that is memory and I/O intense will need to go on a node that can manage the demand

Implemented through the definitions file via labels - the labels will already need to be defined in the cluster

`pod-definition.yaml`
```
apiVerions: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
  - name: data-processor 
    image: data-processor 
  
  nodeSelector: 
    size: Large
```

`kubectl label nodes node01 size=Large`

it does have it's **limitations**, you cannot use conditional logic to schedule pods, only the single label value 

for example, you cannot schedule a pod on a large or medium size node, and you cannot say do not assign on any small nodes - or not small. 

### Node Affinity

ensures pods are hosted on particular nodes. for example, a pod that executes a large job should always run on a pod large enough to execute the jobs while also ensuring good performance 

updates are made to the definitions file 

`NodeAffinity.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: data-processor
    image: data-processor 
  affinity:
    nodeAffinity: 
      requiredDuringSchedulingIgnoreDuringExecution:
        nodeSelectorTerms:
        - matchExpressions: 
          - key: size
            operator: In
            values: 
            - Large
            - Medium
```

nodeAffinity allows you to use conditions. for example, the block above will place the pod on a Large or a Medium node 

```
        - matchExpressions: 
          - key: size
            operator: NotIn
            values: 
            - Small
```

you can also set the operator to NotIn and specify a size that you don't want the pod placed

```
        - matchExpressions: 
          - key: size
            operator: Exits
```

the `exits` operator does not check the values and just checks that one exists 

node affinity types 

available: 

- `requiredDuringSchedulingIgnoreDuringExecution`
- `preferredDuringSchedulingIgnoreDuringExecution`

there are an additional 2 types planned 

planned:

- `requiredDuringSchedulingRequiredDuringExecution`
- `preferredDuringSchedulingRequiredDuringExecution`

|  | `DuringScheduling` | `DuringExecution` | 
|---|---|---|
| `Type 1` | Required | Ignored | 
| `Type 2` | Preferred | Ignored |
| `Type 3` | Required | Required |
| `Type 4` | Preferred | Required |


### Taints and Tolerations vs Node Affinity

can be used together to ensure only the desired pods are scheduled on specific nodes 

### Resource Limits

the default resource request from the scheduler is set to `0.5 cpu` and `256MiB RAM`. these settings can be updated through the pod definitions file 

```
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
```

```
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```

`pod-definition`
```
apiVersion: v1
kind: Pod
metadata: 
  name: myapp
  labels: 
    app: myapp
spec:
  containers:
  - name: myapp
    image: nginx
    ports: 
    - containterPort: 8080
  resources: 
    requests:
      memory: "1Gi"
      cpu: 1
    limit: 
      memory: "2Gi"
      cpu: 2
```
the default limit set by kubernetes are `1 cpu` and `512Mi` of RAM, if a milit is not specified in the definitions file 

kubernetes will throttle pods that exceed cpu limits, and will allow pods to exceed specified limits but will terminate a pod that constantly exceeds it's limits. 

### DaemonSets

daemonsets are similar to replicasets, however, daemonsets run one instance of a pod on a node. When a node is added to a cluster, the daemonset will ensure one instance of a pos is running. When the node is terminated, the daemonset will also remove the pod

managing a monitoring agent on each pod is a good use case

the `kube-proxy` service is another good use case for daemonsets 

`daemonset-definition.yaml`
```
apiVersion: v1
kind: DaemonSet
metadata: 
  name: monitoring-daemon
spec:
  selector:
    matchLabels:
      app: monitoring-agent
  template:
    metadata: 
      labels:
        app: monitoring-agent
    spec:
      containers:
      - name: monitoring-agent
        image: monitoring-agent 
```

`kubectl create -f daemonset-definition.yaml` to create the daemonset

`kubectl get daemonsets` to print current daemonsets 

`kubectl describe daemonsets monitoring-daemon` to see daemonset details 

`kubectl create deployment myapp -n kube-system --image=nginx --dry-run=client -o yaml > daemonset-template.yaml` to create a shell template for the daemonset

edit the template and run `kubectl create -f daemonset-template.yaml`


### Static Pods

the kubelet relies on the kubernetes controlplane to coordinate activities on a node, but what if there was no control plane to instruct the kubelet on what to do?

a kubelet can manage a node independant of the master/controlplane 

you will have to add pod definition files to `/etc/kubernetes/manifest` on the node 

kubelet periodically reads this directory and creates pods on the host 

kubelet will ensure pods are available as specified in the definitions file

if a file is removed from the directory, the kubelet will also delete the pod 

only works for pods since kubelet works at the pod level, deploayments, replicasets, etc.. will not work if added to the directory path 

the path is passed in as a kubelet option 

`--pod-manifest-path=/etc/kubernetes/manifests`

you can also pass in a yaml file that has the directory path defied 

`kubeconfig.yaml`
```
staticPodPath: /etc/kubernetes/manifest/
```

you can use docker commands to check the status of pods since there is no kubeapiserver 

statis pods vs. daemonsets 

| static pods| daemonsets | 
|---|---|
| created by kubelet  | created by kube-apiserver (daemonset controller) |
| deploy controlplane component as static pod  | deploy monitoring & logging agents on nodes |
| ignored by kube-scheduler | ignored by kube-scheduler |


use the `-A` flag to search across all namespaces 

`kubectl get pods -A` 


## Lesson3

### Logging & Monitoring

kubernetes does not have a built-in monitoring solution

you can leverage open source and proprietary monitoring solutions such as 

- prometheus
- metrics server (formerly heapster)
  - can have one per cluster
  - cAdvisor is a process that runs via kubelet to pass metrics information via the kube-apiserver 
  - `minikube addons enable metrics-server` to add metrics server to a minikube cluster 
  - `kubectl top node`
  - `kubectl top pod`
- elastic stack
- datadog 
- dynatrace

### Managing Application Logs

`kubectl logs -f pod container-name` to print container level log messages 

## Lesson4

### Application Lifecycle Management

### Rolling Updates and Rollbacks

when creating a deployment, kubernetes creates a rollout and a corresponding revision version. When an upgrade is applied, a new revision is created, for example, revision 1 and revision 2 that has the most recent changes. This helps track the applications version history and makes it easier to rollback to a prior version, if needed.

`kubectl rollout status deployment/myapp-deployment` to print status 

`kubectl rollout history deployment/myapp-deployment` to see revisions and history of the application 

two types of deployment strategies 

- recreate strategy, will delete existing pods and relaunch pods with new version (results in app downtime)
- rolling update, will delete and recreate the app one pod at a time (no app downtime and is the default setting)

changes are applied  by running `kubectl apply -f *.yaml` 

you can also use the `kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1.1`. the issue with this is the definition file will no tbe updated with the specified version 

`kubectl rollout undo deployment/myapp-deployment` to rollback to a prior version 

you can check rollout and rollback by running `kubectl get replicasets`

summary controls

| operation | command | 
|---|---|
| create| `kubectl create -f deployment.yaml` | 
| get | `kubectl get deployments` | 
| update | `kubectl apply -f deployment.yaml` |
|  | `kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1.1` |
| status | `kubectl rollout status deployment/myapp-deployment` |
|  | `kubectl rollout history deployment/myapp-deployment` |
| rollback | `kubectl rollout undo deployment/myapp-deployment` |


### Commands and Arguments in Docker

unlike a VM, a container is not intended to host an operating system. Containers are intended to run a specific process and then exits

a container is only suppose to like as long as the process running is active 

`docker run --name ubuntu-sleeper ubuntu-sleeper 10` 

```
apiVersion: v1 
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  container:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper
    command: ["sleep"]
    args: ["10"]
```

### Setting an environment variable with kubernetes 

`docker run -e APP_COLOR=pink simple-webapp-color` 

```
apiVersion: v1 
kind: Pod
metadata:
  name: simple-webapp-color
spec:
  container:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper
    ports:
      - ContainerPort: 8080
    env:
      - name: APP_COLOR
        value: pink
```

`configMaps` are used to pass configuration data in the form of key:value pairs 

a configmap can be created imperatively and declaratively

| type| command | 
|---|---|
| imperative | `kubectl create configmap app-config --from-literal=APP_COLOR=blue` | 
| imperative | `kubectl create configmap app-config --from-file=/path/to/file/configmap.yaml` | 
| declarative | `kubectl create -f configmap.yaml` | 

`kubectl get configmaps`

`kubectl describe configmaps`

how to set a configMap declaratively

```
apiVersion: v1 
kind: Pod
metadata:
  name: simple-webapp-color
spec:
  container:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper
    ports:
      - ContainerPort: 8080
    envFrom:
      - configMapRef:
        name: app-config
```

you can also inject a single environment variable 

```
    env:
      - name: APP_COLOR
        valueFrom:
          configMapKeyRef:
          name: app-config
          key: APP_COLOR
```

you can also set through a volume

```
    volumes:
      - name: app-config-volume
        configMap:
          name: app-config
```

### Secrets

you can create secrets imperatively and declaratively

| imperative | declarative | 
|---|---|
| `kubectl create secret generic app-secret --from-literal=key=value` | `kubectl create -f secret.yaml`  |
| `kubectl create secret generic app-secret --from-file=/path/to/file/secret.properties` |  |


`secret.yaml`
```
apiVersion: v1
kind: Secret
metadata: 
  name: app-secret
data:
  DB_HOST: bXlfc3FsCg==
  DB_USER: cm9vdAo=
  DB_PASSWD: cGFzc3dvcmQK
```

the data section stores the values in a hashed encoded format 

to convert raw text to en encoded format run

`echo 'my_sql' | base64` 

`kubectl get secrets` to view system secrets 

`kubectl describe secrets my-secret`

to see secrets hash values run `kubectl describe secret my-secret -o yaml`

to decode secret run 

`echo 'my_sql' | base64 --decode` 

then add the section to the pod definitions file 

```
...
envFrom:
  - secretRef:
    name: app-secret
```

```
...
 env:
  - name: DB_PASSWD
    valueFrom: 
      secretKeyRef:
        name: app-secret
        key: DB_PASSWD
```

```
...
 volumes: 
 - name: app-secret-volume
   secret:
     secretName: app-secret
```

### Multi Container PODs

these containers are created together and destroyed together

run using the same resources and can refer to each other as locahost 

```
...
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: webapp
    image: webapp
    ports: 
      containerPort: 8080
  - name: log-agent
    image: log-agent
```
execute command on a container 

`kubectl -n elastic-stack exec -it app -- cat /log/app.log`

`kubectl logs app -n elastic-stack`

But at times you may want to run a process that runs to completion in a container. For example a process that pulls a code or binary from a repository that will be used by the main web application. That is a task that will be run only one time when the pod is first created. Or a process that waits for an external service or database to be up before the actual application starts. That’s where `initContainers` comes in.

An initContainer is configured in a pod like all other containers, except that it is specified inside a `initContainers` section, like this:

 
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'git clone <some-repository-that-will-be-used-by-application> ;']
``` 

When a POD is first created the initContainer is run, and the process in the initContainer must run to a completion before the real container hosting the application starts.


If any of the initContainers fail to complete, Kubernetes restarts the Pod repeatedly until the Init Container succeeds.

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```

## Lesson5

### Cluster Maintenance

### OS Upgrades

the default pod eviction timeout is 5 minutes 

`kube-controller-manager --pod-eviction-timeout=5m0s`

if you know a node will not be back online, you can drain the node of the workload 

`kubectl drain node-01`

kubernetes will recreate pods on other nodes 

the node is also marked as `unschedulable` so no pods are scheduled on the node 

once the node is back online you can `kubectl uncordon node-01` so the node is not able to host pods 

`kubectl cordon node-01` will mark the pod as unschedulable but will not terminate the workload 


### Kubernetes Software Versions

you can get your current kubernetes version by running `kubectl get nodes` 

kubernetes versions are divided into three parts 

for example, v1.11.3

| v1.|11. | 3|
|---|---|---|
|major  |  minor | patch|
|  | features | bug fixes |
|  | functionality |  |

kubernetes follows a standard software versioning procedure. 

New features and functionalities are released every month through minor releases 

kubernetes supports stable, alpha and beta releases 

other projects maintain separate version numbers 

for example, etcD and CoreDNS have different versioning numbers 

### Cluster Upgrade Introduction

controlplane components can have different versions, it is not mandatory for the versions to be the same for all components 

because the `kube-apiserver` is the core of the control plane, none of the other components should be on a high version 

the `controller-manager` and `kube-scheduler` can be one version below 

the `kubelet` and `kube-proxy` can be up to two versions below 

the `kubectl` utility can be one version above and below the `kube-apiserver` 

when upgrading versions, it is recommended to upgrade one minor version at a time 

upgrading a cluster involves two major steps 

first you upgrade your master node, then the worker nodes 

when the master node goes down momentarily, this does not affect the worker nodes. worker nodes will continue to operate. 

the master functions will not be accessible during the version upgrade process, this also means the scheduler is affected as well. if a node goes down, a new node will not be scheduled 

Two strategies to upgrade worker nodes 

1. you can upgrade all the nodes at once; requires downtime. this will bring worker services down 

2. you can upgrade nodes one at a time; requires no downtime. you can move workloads to other nodes while performing the upgrade. 

3. add new upgraded nodes to the cluster, move workload, and decommission old nodes; requires no downtime 

`kubeadm upgrade plan` provides a printout of useful information for planning your upgrade 

you have to upgrade `kubeadm` prior to upgrading the cluster 

upgrading the controlplane example: 

```
On the controlplane node, run the command run the following commands:

apt update
This will update the package lists from the software repository.

apt install kubeadm=1.20.0-00
This will install the kubeadm version 1.20

kubeadm upgrade apply v1.20.0
This will upgrade kubernetes controlplane. Note that this can take a few minutes.

apt install kubelet=1.20.0-00 This will update the kubelet with the version 1.20.

You may need to restart kubelet after it has been upgraded.
Run: systemctl restart kubelet
```

example upgrading node 

```
On the node01 node, run the command run the following commands:

If you are on the master node, run ssh node01 to go to node01


apt update
This will update the package lists from the software repository.


apt install kubeadm=1.20.0-00
This will install the kubeadm version 1.20


kubeadm upgrade node
This will upgrade the node01 configuration.


apt install kubelet=1.20.0-00 This will update the kubelet with the version 1.20.


You may need to restart kubelet after it has been upgraded.
Run: systemctl restart kubelet
```

### Backup and Restore Methods

the preferred way to manage a kubernetes cluster is by using declarative definition files and source control 

you can also query the `kube-apiserver`, which stores all configuration information in the cluster 

`kubectl get all --namespaces -o yaml > all-deployment-services.yaml`

etcd data backups can be taken from the data directory that was configured at the time of creation or from the built in `etcdctl` utility 

## Lesson6

### Security

### Kubernetes Security Primitives

the hosts used to deploy kubernetes must be secured, root access and username and password authentication should be disabled, the hosts should only allow ssh-based authentication  

the `kube-apiserver` is the central hub of a kubernetes cluster and can perform almost any operation. Therefore, it's important that the `kube-apiserver` is secured 

two questions you need to answer: 

| Who can access the server? | What can they do? |
|---|---|---|
| Defined by authentication  | RBAC authorization | |
| Files - username and password | ABAC authorization |  |
| Files - username and tokens  | Node authorization |  |
| Certificates | Webhook mode |  |
|  External authentication providers - LDAP |  |  |
| Service accounts  |  |  |

all communication within the cluster uses TLS encryption 

by default all pods have access to other pods within the cluster, you can restrict access using network policies 

### Authentication

kubernetes does not manage user accounts natively, it relies on a third-party identity management system, certificates, or file-based with user and role information 

you cannot create or view a list of users within the cluster 

however, you can create and manage service accounts in kubernetes 

all user access is managed by the `kube-apiserver` 

the `kube-apiserver` will authenticate a request before processing the request 

several authentication mechanisms include: 

* static password file 
* static token file 
* certificates 
* identity services 

for static files, you will need to add the specification `--basic-auth-file=user-details.csv` to `kube-apiserver` and restart the service 

if `kubeadm` was used, you must update the pod definition file with `--basic-auth-file=user-details.csv`. `kubeadm` tool will automatically restart the `kube-apiserver` once the file is updated 

use `--token-auth-file=user-details.csv` if you're using tokens

### TLS Basics

a certificate is used to guarantee trust between two parties during a transaction  

certificates ensures the communication between the user and the server is encrypted and that the server is who it says it is

data are sent in plain text format when accessing non-tls websites 

symmetric encryption uses the same key to encrypt and decrypt data, still a risk of a hacker accessing the details of the key 

asymmetric encryption uses a pair of keys to encrypt the data: a public and private key 

run `ssh-keygen` to generate the private and public key pairs for asymmetric encryption

you can also generate symmetric private and public keys 

`openssl genrsa -out my.key 1024` generates private key

`openssl rsa -in my.key -pubout > my.pem` generates public key 

`openssl req -new -key my.key -out my.csr -subj "/C=US/ST=CA/O=MyOrg, Inc./CN=my-org.com"` generates a CSR that will need to go to a CA


| public keys | private keys |
|---|---|---|
| *.crt *.pem |  *.key or *-key.pem| |
| server.crt | server.key |
| server.pem | server-key.pem |
| client.crt | client.key |
| client.pem | client-key.pem |

### TLS in Kubernetes

three types of certificates 

* root certificates: usually setup with the CA
* server certificates: a/symmetric encryption 
* client certificate: usually managed within the browser  

all interactions within the kubernetes cluster and with the client are secured 

kubernetes will use server certificates and client certificates 


| certificate type | server type | server | keys |
|---|---|---|---|
| server certificate | master node | kube-apiserver | apiserver.crt apiserver.key |
| server certificate | master node | etcd | etcdserver.crt etcdserver.key |
| server certificate | worker node | kubelet | kubelet.crt kubelet.key |
| client certificate | client admin user | client to kube-apiserver | admin.crt admin.key |
| client certificate | master node | kube-scheduler to kube-apiserver | scheduler.crt scheduler.key |
| client certificate | master node | kube-controller-manager to kube-apiserver | controller-manager.crt controller-manager.key |
| client certificate | master node | kube-proxy to kube-apiserver | proxy.crt proxy.key |
| client certificate | master to worker node | kube-apiserver to etcd-server | api-etcd.crt api-etcd.key |
| client certificate | master to worker node | kube-apiserver to kubelet | api-kubelet.crt api-kubelet.key |

### TLS in Kubernetes – Certificate Creation

common tools to generate certificates: `easyrsa`, `openssl`, `cfssl`

generating certificates 

| certificate type | step | command |
|---|---|---|
| server certificate | generate CA key |  `openssl genrsa -out ca.key 2048` | 
| | generate CSR |  `openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr` | |
| | sign certificates |  `openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt` | |
| client certificate | generate CA key |  `openssl genrsa -out admin.key 2048` | 
| | generate CSR w/ admin group `masters` |  `openssl req -new -key admin.key -subj "/CN=kube-admin/O=system:masters" -out admin.csr` | |
| | sign certificates |  `openssl x509 -req -in admin.csr -CA ca.csr -CAkey ca.key -out admin.crt` | |

service account certificates must be prefixed with `system`, e.g., `system:kube-scheduler`

you can use the generated certificates to access the `kube-apiserver` 

`curl https://kube-apiserver:6443/api/v1/pods --key admin.key --cert admin.crt --cacert ca.crt `

`kube-config.yaml`

```
apiVersion: v1
clusters:
- cluster:
    certificate-authority: ca.crt
    server: https://kube-apiserver:6443
  name: kubernetes 
kind: config 
users: 
- name: kubernetes-admin
  user:
    client-certificate: admin.crt
    client-key: admin.key

```

the ca.crt root certificate will need to be copied to all nodes and services 

name kubelet certificates after the node, e.g., node01, node02, etc...

### View Certificate Details


certificate information can be fount in one of two places, depending on how the environment was setup

| the hard way | kubeadm |
|---|---|---|
| `cat /etc/systemd/system/kube-apiserver.service` |  `cat /etc/kubernetes/manifest/kube-apiserver.yaml`| |

use the openssl utility to view certificate details 

`openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout`

### Certificates API

The server where you create the ca.crt file becomes the CA server, e.g., if you created the ca.crt on the master node, then the master node becomes the CA server within the cluster 

kubernetes has a built-in certificates api that will manage certificates and ca within the cluster 

you send a certificate request to the kubernetes certificates api, by doing this the admin does not have to manually log into the master node 

the kubernetes certificate api will create a certificate signing request object 

one the certificate signing request object is created it can be seen by all admins of the cluster 

that means the requests can be reviewed and approved through the `kubectl` tool and the contests of the certificate extracted and shared with the users 

| step | command |
|---|---|---|
| admin generate key |  `openssl genrsa -out me.key 2048`| |
| admin generates csr |  `openssl req -new -key me.key -subj "/CN=me" -out me.csr`| |

the certificate signing request object is created with a manifest file 

the key is not specified via plain text, it must be encoded using base64 

`cat me.csr | base64 | tr -d "\n"`

`me.yaml`
```
apiVersion: v1
kind: CertificateSigningRequest
metadata:
  name: me
spec:
  groups: 
  - system:authenticated 
  usage:
  - digital signature
  - key encipherment
  - server auth 
request:
  <add base64 encoded key here>
```

admins can run `kubectl get csr` to see all certificate signing requests 

approve requests by running `kubectl certificate approve me`

view certificate through yaml format 

`kubectl get csr me -o yaml`

NOTE: the key is in base64 format and will need to be decoded 

`echo my key | base64 --decode `

the kubernetes controller manager handles all of the certificate tasks 

use `kubectl create -f me.yaml` to create object 

### KubeConfig

the kubeconfig file is located at `$HOME/.kube/config`

the file has three sections: 

   * clusters: prod, development, test, google, azure
   * users: user accounts for which we have access to the various clusters and environments 
   * contexts: combines the cluster and user together, e.g., which cluster will be accessed by which account - admin@production 

the kubeconfig file is in yaml format 

`kubeconfig.yaml`
```
apiVersion: v1
kind: Config

clusters:

- name: my-kube-playground
  cluster: 
    certificate-authority: ca.crt
    server: https://my-kube-playground:6443

contexts:

- name: my-kube-admin@my-kube-playground
  context:
    cluster: my-kube-playground
    user: my-kube-admin
    namespace: finance


users:

- name: my-kube-admin
  user:
    client-certificate: admin.crt
    client-key: client.key

```

run `kubectl config view` to view the kubeconfig file and the current context 

you can also specify a kubeconfig file by passing in `--kubeconfig=/path/to/my-custom-config`

to change contexts between clusters run `kubectl config use-context prod-user@production`

you can specify the namespace value in the yaml file to manage environments with multiple clusters and namespaces 

you can also pass in the base64 encoded certificate in the yaml file compared to the full path and name of the certificate 

`kubectl config --kubeconfig=/root/my-kube-config use-context research` to change current context

`kubeconfig.yaml`
```
apiVersion: v1
kind: Config

clusters:

- name: my-kube-playground
  cluster: 
    server: https://my-kube-playground:6443
    certificate-authority-data: <base64 encoded key>
```

### API Groups

you can interact with the kubernetes api via curl

`curl https://kube-master:6443/version` to get the current kubernetes version

`curl https://kube-master:6443/api/v1/pods` to print a list of pods in the cluster 

list of kubernetes api groups 

| group | actions |
|---|---|---|
| `/metrics` | monitor the cluster | |
| `/healthz` | monitor the health of the cluster  | |
| `/version` | prints kubernetes version | |
| `/api` | core group  | |
| --`/v1` |  | |
|  | namespaces  | |
|  | pods  | |
|  | rc  | |
|  | events  | |
|  | endpoints  | |
|  | nodes  | |
|  | bindings  | |
|  | PV  | |
|  | PVC  | |
|  | configmaps  | |
|  | secrets | |
|  | services  | |
| `/apis` | named group | |
| --`/apps` | api group | |
| ----`/v1` |  | |
| ------`/deployments` | resource | |
| ------`/replicasets` | resource | |
| ------`/statefulsets` | resource | |
| --------`list` | verb | |
| --------`get` | verb | |
| --------`create` | verb | |
| --------`delete` | verb | |
| --------`update` | verb | |
| --------`watch` | verb | |
| --`/extensions` | api group | |
| --`/networking.k8s.io` | api group | |
| --`/storage.k8s.io` | api group | |
| --`/authentication.k8s.io` | api group | |
| --`/certificates.k8s.io` | api group | |
| `/logs` | integrating with 3rd party logging applications  | |


you can use curl to list api groups 

`curl https://kube-master:6443/ -k`

`curl https://kube-master:6443/apis -k | grep name`

you will need to pass in your certificates to access the kubernetes api server

```
--key admin.key
--cert admin.crt
cacert ca.crt
```

you can also launch a `kubectl proxy` session that will use the credentials and certificates from your kubeconfig file 

the service will launch locally on port 8001

you can then use curl on port 801 to list the api services 

`curl https://kube-master:8001/ -k`


### Authorization

kubernetes supports several authorization types 

* node
* abac (attribute based access control)
* rbac (role based access control)
* webhook (through 3rd party authorization app)
* always allow
* always deny

it's set to allwaysallow by default 

update the `--authorization-mode=` setting to update the cluster's authorization type 

you can list more than one authorization type separated by comma

kubernetes will attempt to authorize using each mode until the user is grated access or the exhausts the list 

### Role Based Access Controls

rbac is set through role objects 

`developer-role.yaml`
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list","get","create","update","delete"]

- apiGroups: [""]
  resources: ["ConfigMap"]
  verbs: ["create"]
```

you can add `resourceNames: ["blue","yellow"]` to the definitions file to further restrict access to specific resources within the resource 

leaving the apiGroup blank will default to the core api group 

run `kubectl create -f developer.yaml` to create the role 

create a role binding object to link a user to the new role 

this is also done through a yaml file 

`devuser-developer-binding.yaml`
```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: devuser-developer-binding 
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef: 
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io

```

create the role binding with `kubectl create -f devuser-developer-binding.yaml`

run `kubectl get roles` to print list of roles 

run `kubectl get rolebindings` to print list of role bindings

`kubectl describe role developer` to print details about a role 

`kubectl describe rolebinding devuser-developer-binding` to print details about role bindings

you can use the `kubectl auth can-i create deployments` to get a quick notice on whether you have permission to perform a specific function on the cluster 

use `kubectl auth can-i create deployments --as dev-user` to test access for a specific user without needing to log in as the user 

add `--namespace test` to test authorization within a specific namespace 


### Cluster Roles

roles and rolebindings are namespaces, meaning, they are created within specific namespaces 

if you do not specify a namespace, the role and rolebinding will be created within the default namespace 

recourses on the cluster are categorized as either a namespace resource or a cluster-scoped resource 

nodes are a cluster-scoped resource whereas pods and roles are namespaced resources 


| namespaced | cluster-scoped |
|---|---|---|
| pods | nodes | |
| replicasets | PV | |
| jobs | clusterroles | |
| deployments | clusterrolebindings | |
| services | namespaces | |
| secrets | certificatesigningrequests | |
| roles |  | |
| rolebindings |  | |
| configmaps |  | |
| PVC |  | |

run the following for a comprehensive list 

namespaced: `kubectl api-resources --namespaced=true`

cluster scoped: `kubectl api-resources --namespaced=false`


`cluster-admin-role.yaml`
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-administrator
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list","get","create","delete"]

```

then create a clusterrolebinding, similar to the rolebinding 


`cluster-role-admin-binding.yaml`
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-role-binding 
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef: 
  kind: ClusterRole
  name: cluster-administrator
  apiGroup: rbac.authorization.k8s.io

```


### Service Accounts

kubernetes has two kids of accounts: users and service accounts 

`kubectl create serviceaccount service-account-name` to create a service account 

`kubectl get serviceaccount` to view a list of service accounts 

kubernetes automatically creates a token when the service account is created. the token is used to authenticate to the kube-apiserver

`kubectl describe serviceaccount service-account-name`

kubernetes will store the token in a secret object 

`kubectl describe secret service-account-name-token-name` to view the token 

### service accounts 1.22 & 1.24 update 

this update changed the way service accounts and secrets work 

every namespace gets a default service account that has an associated token

`kubectl get serviceaccount`

when a pod is created the service account is automatically associated to the pod and mounted at a well-known location 

run `kubectl describe pod my-kubernetes-dashboard` and look under the `mounts` section 

`kubectl exec -it my-kubernetes-dashboard ls /var/run/secrets/kubernetes.oi/serviceaccount`


`kubectl exec -it my-kubernetes-dashboard cat /var/run/secrets/kubernetes.oi/serviceaccount/token`

you can decode the token using this command 

`jq -R 'split (".") | select(length > 0) | .[0],.[1] | @base64 | fromjson'  <<< <enter token> `

you can also copy and paste the token in the [jwt.io](https://jwt.io/) website 

prior to v1.22 tokens did not have an expiration date and were not bound to any service 

v1.22 brought a tokenRequestAPI that enabled audience bound, time bound, and object bound

pods now receive a token from the token api 

`kubectl get pod my-kubernetes-dashboard -o yaml`

v1.24, reduction of secret-based service account tokens 

`kubectl create serviceaccount dashboard-sa` 

tokens are no longer auto-generated post v1.24

you must create the token by running: `kubectl create token dashboard-sa`


### Image Security

for example, take a pod definition file 


`nginx-pod.yaml`
```
api: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

the `image` line is actually calling `image: docker.oi/library/nginx`


| docker.oi/ | library/ | nginx|
|---|---|---|
| registry  |  user account | image repository |


to use a private registry 

`docker login private-registry.io`

`docker run private-registry.io/apps/internal-app`

add image path to the image line

use the following to authenticate 

```
kubectl create secret docker-registry regcred \
  --docker-server=private-registry.io
  --docker-username=user
  --docker-password=password
  --docker-email=user@email.com
```

update the definition file

`nginx-pod.yaml`
```
api: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
  imagePullSecret: 
    - name: regcred
```

you can check the different types of secrets you can create by running `kubectl create secret`

### Security Contexts

container security 

`docker run --user=1001 ubuntu sleep 3600` 

`docker run --cap-add MAC_ADMIN ubuntu`

in kubernetes, containers are encapsulated within pods

you can configure security settings at the container level or pod level 

adding the `securityContext` under specs to apply at the pod level 

`pod-definition.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  securityContext:
    runAsUser: 1000
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep","3600"]
```

adding the `securityContext` under container to apply at the container level. you can also add a `capabilities` section

`pod-definition.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep","3600"]
      securityContext:
        runAsUser: 1000
      capabilities: 
        add: ["MAC_ADMIN"]
```

hint: run `kubectl exec ubuntu-sleeper -- whoami` to check the user who is running the container 


### Network Policies

network policy that allows ingress from an API pod to a database server 

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy 
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          name: api-pod
    ports:
    - protocol: TCP
      port: 3306
```

network policies are enforced by the network solution on the cluster, not all solutions support network policies 

| supported| not supported |
|---|---|---|
| kube-route  |  flannel | 
| calico  |   | 
| romana  |   | 
| weave-net  |   | 


By default kubernetes allows all pods to communicate with other pods within the cluster. You have to create a network policy to block any unwanted network traffic to specific pods. you do this by associated a network policy to a pod(s) 

This network policy will block all traffic to the database pod except traffic from the api-pod 

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy 
spec: 
  podSelector: 
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    podSelector: 
      matchLabel:
        name: api-pod
    ports: 
    - protocol: TCP
      ports: 3306
  
```

you can add the `namespaceSelector` section to the network policy definition to allow communication of pods across different namespaces

you can also allow traffic from outside servers to access pods by adding the `ipBlock` definition

```
ipBlock:
  cidr: 192.168.5.10/32
```


## Lesson7

### Storage

#### Introduction to Docker Storage

a good understanding of how storage works with containers will help with understanding how storage works in kubernetes 

there are two concepts when it comes to storage in docker 


| storage drivers | volume drivers |
|---|---|---|
| aufs | local |
| zfs | azure file storage|
| btrfs | convoy |
| device mapper | flocker |
| overlay | glusterFS |
| overlay2 | NetApp |


#### Storage in Docker

docker creates and stores data in `/var/lib/docker`

```
/var/lib/docker
 aufs
 containers
 image
 volumes
```

you should be familiar with the docker layer architecture - how docker containers are build 

volume mounting - mounts a volume to the `/var/lib/docker/volumes` directory 

bind mounting - mount a folder on the host and make it accessible within the container 

mounting commands 

`docker run -v data_volume:/var/lib/mysql mysql`

`docker run -v data_volume2:/var/lib/mysql mysql`

`docker run -v /data/mysql:/var/lib/mysql mysql`

`docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql`

#### Container Storage Interface

docker used to be the container runtime engine of choice and was thus embedded within the kubernetes source code

however, to enable the use of other container runtime engines, the container runtime interface was created to extend to other container runtime platforms without the need to change source code 

same concept with the container networking interface, or CNI.

the container storage interface, or CSI, as well. You can develop your own storage drivers based on the standards put forth by the interfaces 

CSI Remote Procedure Calls, or RPCs

| csi definition | command | driver |
|---|---|---|
| should call to provision a new volume | CreateVolume | should provision a new volume on the storage |
| should call to delete a volume | DeleteVolume | should decommission a volume |
| should call to place a workload that used the volume onto a node | ControllerPublishVolume | should make the volume available on a node |


#### Volumes

the below definition file mounts an external storage volume from /data to the container filesystem at /opt

volumeMount.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: number-generator
spec:
  containers:
  - image: alpine
    name: alpine
    command: ["/bin/sh","-c"]
    args: ["shuf -i 0-100 -n 1 >> /opt/number.out"]
    volumeMounts:
    - mountPath: /opt
      name: data-volume

  volumes:
  - name: data-volume
    hostPath: 
    - path: /data
      type: Directory 

```

#### Persistent Volumes

pv-definition.yaml
```
apiVersion: v1
kind: PersistentVolume
metadata: 
  name: pv-vol1
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 1Gi
  hostPath:
    path: /tmp/data
```

`kubectl create -f pv-definition.yaml`

`kubectl get persistentvolume`

you can add storage specific definitions as well
```
awsElasticBlockStore:
  volumeID: <vol ID>
  fsType: ext4
```


#### Persistent Volume Claims

PV and PVC are two different objects int he kubernetes namespace 

an administrator creates a set of PVs and a user creates a PVC to use the storage 

once the PVC is creates, kubernetes binds the PVC to the PV

there is a one-to-one relationship between a PVC and a PV

pvc-definition.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessMode:
  - readWriteOnce
  resources: 
    requests: 
      storage: 500Mi
```

`kubectl create -f pvc-definition.yaml`


`kubectl get persistentvolumeclaim`

`kubectl delete persistentvolumecliam myclaim`

the default is set to `persistentVolumeReclaimPolicy: Retain`, which will persist the hold on the storage 

the default is set to `persistentVolumeReclaimPolicy: Delete` will make the volume available for use by users 


Once you create a PVC use it in a POD definition file by specifying the PVC Claim name under persistentVolumeClaim section in the volumes section like this:

``` 
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

helpful commands: 

`kubectl exec webapp -- cat /log/app.log` view logs 

#### Storage Class

supports dynamic provisioning where you do not have to manually create the PV

sc-definition.yaml
```
apiVersion: storage.k8s.io/va
kind: StorageClass
metadata:
  name: google-storage

provisioner: kubernetes.io/gce-pd

parameters:
  type: pd-standard
  replication-type: none  
```

add the storage class to the `pvc-definition.yaml` file

pvc-definition.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessMode:
  - readWriteOnce
  storageClass: google-storage
  resources: 
    requests: 
      storage: 500Mi
```

storage classes allow you to create different classes of service, such as a bronze | silver | gold class with different configurations 

## Lesson8

### Networking

#### Networking Introduction

#### Prerequisite Switching, Routing, Gateways CNI in kubernetes

- switching and routing

  - switching
    - connects two or more networks 
    - use `ip link` to view the interface 
    - assign addresses to the compute instances `ip addr add 192.168.1.10/24 dev eth0`
    - a switch can only enable communication within a network, meaning, it can only receive and pass packets to hosts on the same network 

  - routing
    - helps connect two or more networks together 

  - default gateway 
    - is the door to the outside world, to other networks 
    - run `route` to see the system's routing table 
    - add a gateway between networks `ip route add 192.168.2.0/24 via 192.168.1.1`
    - routing has to be setup on all system routing tables 
    - a host machine can also serve as a gateway to a server on another network 
    - update packet forwarding `cat /proc/sys/net/ipv4/ip_forward` is set to 0 as default
    - `echo 1 > /proc/sys/net/ipv4/ip_forward` to set it to 1 to enable packet forwarding 
    - to persist change update the value in the `/etc/sysctl.conf` file `net.ipv4.ip_forward = 1`


- DNS
  - DNS configurations on linux
    - use the `/etc/hosts` file to set local host names
    - no validation is done using this method
    - this method is called name resolution and works within a small network of systems 
    - managing this locally became problematic and is why the DNS server came about
    - the DNS server provides a centralized way to manage all the entries  
    - `cat /etc/resolve.conf` -> `nameserver    192.168.1.100 [ip of the DNS server]` 
    - the order of resolution is managed by the `cat /etc/nsswitch.conf` file 
    - `hosts:    files dns` will search the local hosts file first then dns

  - CoreDNS introduction
    - serves as a local DNS server 
    - ip to hostname mappings are stored in the `/etc/hosts` file

- network namespaces
  - used by containers to implement network isolation 
  - to create a new namespace on a linux host run `ip netns add red` command 
  - `ip netns` to list namespaces 
  - to view an interface within the namespace run `ip netns exec red ip link` or `ip -n red link` 
  - to connect two namespaces run `ip link add veth-red type veth peer name veth-blue`
  - now attach the peer to the appropriate namespace `ip link set veth-red netns red` and `ip link set veth-blue netns blue`
  - then assign an ip to each namespace `ip -n red addr add 192.168.15.1 dev veth-red` and `ip -n blue addr add 192.168.15.2 dev dev veth-blue` 
  - then bring up each interface `ip -n red link set veth-red up` and `ip -n blue link set veth-blue up`
  - to enable this for many namespaces you will need to setup a virtual switch, common solutions linux bridge and open vSwitch
  - add a new interface bridge `ip link add v-net-0 type bridge`
  - connect all the virtual cables to each namespace and to the new bridge interface 
  - `ip link set veth-red netns red` and `ip link set veth-red-br master v-net-0`
  - `ip link set veth-blue netns blue` and `ip link set veth-blue-br master v-net-0`
  - set ip address and turn them on
  - `ip -n red addr 192.168.15.1 dev veth-red` and `ip -n red link set veth-red up`
  - `ip -n blue addr 192.168.15.2 dev veth-blue` and `ip -n blue link set veth-blue up`
  - the host serves as the gateway for the namespace networks 
  - add a route to the host `ip netns exec blue ip route add 192.168.1.0/24 via 192.168.15.5` 
  - enable NAT so packets can be sent back to the namespace `iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE` to send packets to the outside using it's own ip and not the ip of the namespace
  - point namespace to the host to connect to the outside via the host default gateway `ip netns exec ip route add default via 192.168.15.5`
  - for outside traffic going to the namespace, use port forwarding `iptables -t nat -A PREROUTING --dport 80 --to-destination 192.168.15.2:80 -j DNAT`
  - ensure you set the netmask if you are not able to ping one namespace to the other 
  - also check the firewall table 

- docker namespaces 
- CNI
  - a common standard for container run times
  - docker has it's own standard called CNM


## Lesson8

#### Cluster Networking

### Networking

#### Networking Introduction

#### Prerequisite Switching, Routing, Gateways CNI in kubernetes

- switching and routing

  - switching
    - connects two or more networks 
    - use `ip link` to view the interface 
    - assign addresses to the compute instances `ip addr add 192.168.1.10/24 dev eth0`
    - a switch can only enable communication within a network, meaning, it can only receive and pass packets to hosts on the same network 

  - routing
    - helps connect two or more networks together 

  - default gateway 
    - is the door to the outside world, to other networks 
    - run `route` to see the system's routing table 
    - add a gateway between networks `ip route add 192.168.2.0/24 via 192.168.1.1`
    - routing has to be setup on all system routing tables 
    - a host machine can also serve as a gateway to a server on another network 
    - update packet forwarding `cat /proc/sys/net/ipv4/ip_forward` is set to 0 as default
    - `echo 1 > /proc/sys/net/ipv4/ip_forward` to set it to 1 to enable packet forwarding 
    - to persist change update the value in the `/etc/sysctl.conf` file `net.ipv4.ip_forward = 1`


- DNS
  - DNS configurations on linux
    - use the `/etc/hosts` file to set local host names
    - no validation is done using this method
    - this method is called name resolution and works within a small network of systems 
    - managing this locally became problematic and is why the DNS server came about
    - the DNS server provides a centralized way to manage all the entries  
    - `cat /etc/resolve.conf` -> `nameserver    192.168.1.100 [ip of the DNS server]` 
    - the order of resolution is managed by the `cat /etc/nsswitch.conf` file 
    - `hosts:    files dns` will search the local hosts file first then dns

  - CoreDNS introduction
    - serves as a local DNS server 
    - ip to hostname mappings are stored in the `/etc/hosts` file

- network namespaces
  - used by containers to implement network isolation 
  - to create a new namespace on a linux host run `ip netns add red` command 
  - `ip netns` to list namespaces 
  - to view an interface within the namespace run `ip netns exec red ip link` or `ip -n red link` 
  - to connect two namespaces run `ip link add veth-red type veth peer name veth-blue`
  - now attach the peer to the appropriate namespace `ip link set veth-red netns red` and `ip link set veth-blue netns blue`
  - then assign an ip to each namespace `ip -n red addr add 192.168.15.1 dev veth-red` and `ip -n blue addr add 192.168.15.2 dev dev veth-blue` 
  - then bring up each interface `ip -n red link set veth-red up` and `ip -n blue link set veth-blue up`
  - to enable this for many namespaces you will need to setup a virtual switch, common solutions linux bridge and open vSwitch
  - add a new interface bridge `ip link add v-net-0 type bridge`
  - connect all the virtual cables to each namespace and to the new bridge interface 
  - `ip link set veth-red netns red` and `ip link set veth-red-br master v-net-0`
  - `ip link set veth-blue netns blue` and `ip link set veth-blue-br master v-net-0`
  - set ip address and turn them on
  - `ip -n red addr 192.168.15.1 dev veth-red` and `ip -n red link set veth-red up`
  - `ip -n blue addr 192.168.15.2 dev veth-blue` and `ip -n blue link set veth-blue up`
  - the host serves as the gateway for the namespace networks 
  - add a route to the host `ip netns exec blue ip route add 192.168.1.0/24 via 192.168.15.5` 
  - enable NAT so packets can be sent back to the namespace `iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE` to send packets to the outside using it's own ip and not the ip of the namespace
  - point namespace to the host to connect to the outside via the host default gateway `ip netns exec ip route add default via 192.168.15.5`
  - for outside traffic going to the namespace, use port forwarding `iptables -t nat -A PREROUTING --dport 80 --to-destination 192.168.15.2:80 -j DNAT`
  - ensure you set the netmask if you are not able to ping one namespace to the other 
  - also check the firewall table 

- docker namespaces 
- CNI
  - a common standard for container run times
  - docker has it's own standard called CNM

#### Cluster Networking

kubernetes clusters consist of master wnd worker nodes 

each node must have an interface connected to a network

each interface must have an ip address assigned 

each host must have a unique hostname and mac address 

the master node kube-api server accepts calls via port 6443

link to list of [required ports](https://kubernetes.io/docs/reference/networking/ports-and-protocols/)

| helpful network troubleshooting commands | 
|---|
| `ip link` |
| `ip addr` |
| `ip link show eth0` |
| `ip addr add 192.168.1.10/24 dev eth0` |
| `ip route` |
| `ip route add 192.168.1.0/24 via 192.168.2.1` |
| `route` |
| `cat /proc/sys/net/ipv4/ip_forward` |
| `arp` |
| `netstat -plnt` |

links to deploy network addons 

https://kubernetes.io/docs/concepts/cluster-administration/addons/

https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-networking-model

#### Pod Networking

kubernetes pod networking model

- every pod should have an ip address
- every pod should be able to communicate with every other pod in the same node 
- every pod should be able to communicate with every other pod on other nodes without NAT

you can solve this problem in a similar way it's solved on a linux server 


| description | command |
|---| ---|
| create bridge on each node | `ip link add v-net-0 type bridge`  |
| bring bridge up  | `ip link set dev v-net-0 up`  |
| assign ip address to the bridge interface  | `ip addr add 10.244.1.1/24 dev v-net-0` |
| create a virtual cable  |  `ip link add ...` |
| attach each end of the pair to the interfaces  | `ip link ser ...`  |
| assign ip address  | `ip -n <namespace> addr add ...`  |
| add route  | `ip -n ,namespace> route add ...`  |
| bring up the interface  | `ip -n ,namespace. link set`  |
| add routing so pods can communicate across nodes  | `ip route add 10.244.2.2 via 192.168.1.12` |


use CNI to automate networking when creating a container 

kubelet is responsible to container creation and deletion 

kubelet leverages the following file to find the script location, which is passed as a commandline argument: `--cni-conf-dir=/etc/cni/net.d`

then looks in: `--cni-bin-dir=/etc/cni/bin`

`./net-script.sh add <container> <namespace>`

#### CNI in kubernetes

- container runtime must create network namespace
- identify network the container must attach to
- container runtime to invoke network plugin bridge when container is added 
- container runtime to invoke network plugin bridge when container is deleted 
- json format of the network configuration 

kubelet is responsible for running the required scripts to enable container/pod networking 

#### CNI weave

uses an agent to facilitate network typology 

deployed as a deamonset on the cluster 

#### ipam weave

the CNI is responsible for assigning ip addresses to containers 

#### Service Networking

when a service is created, the service is able to access other pods across the cluster no matter which node a pod resides or if it's on a different network. these are generally called clusterIPs

you can make a pod accessible outside the cluster through NodePorts

NodePorts also expose applications across all nodes via the same port number 

services are a cluster-wide object 

kube-proxy can use userspace, ipvs or iptables to setup networking rules 

the default method is iptables 

`kube-proxy --proxy-mode userspace | iptables | ipvs` to set mode

default is iptables 

`iptables -L -t nat | grep db-service` to view rules set by kube-proxy 


#### Cluster DNS

kubernetes deploys a built in DNS server by default 

kubernetes will generate an entry in the cluster DNS whenever a service is created 

once a cluster DNS record is generated, you can refer to each service within the same namespace by using the service name, i.e., http://web-service

If a service is in a different namespace, you will need to use the service name and namespace, i.e., http://web-service.app (the web-service is in the app namespace)

kubernetes will group all services together in a sub-domain called svc (or service), i.e., http://web-service.app.svc

kubernetes clusters all services and pods into a root domain, cluster.local by default, i.e., http://web-service.app.svc.cluster.local

DNS records for pods are not created by default but can be enabled 

for pod records, kubernetes uses the IP and replaces the . with a - by default, i.e., http://10-244-2-5.apps.pod.cluster.local


#### CoreDNS in Kubernetes

you could add an entry into each /etc/hosts file that points to each pod, however, that would be tedious

kubernetes adds an entry into the /etc/resolve.conf file that points to a centralized DNS service within the cluster 

kube-dns was deployed in versions prior to 1.12

CoreDNS is used post version 1.12

CoreDNS is deployed as a pod in the kube-system namespace 

It's deployed as a replicaset for redundancy 

CoreDNS uses a file named CoreFIle located at `/etc/coredns/Corefile`

CoreDNS plug-in allows CoreDNS to work in kubernetes, the top-level domain name for the cluster is set within the Corefile

this is also where you can enable pod hostname creation using the ip with dashes 

the Corefile is passed into the pod as a configmap, that allows for configuration updates 

kubelet is responsible for adding the CoreDNS service to pods 

the resolve.conf file includes a search entry that includes the namespace, service, and detaul cluter domain, i.e., `search default.svc.cluster.local svc.cluster.local cluster.local`

that only to search services, you cannot search for pods the same way

for pods, use the FQDN, i.e., http://10-244-2-5.default.pod.cluster.local

#### Ingress

point DNS server to the IP of the node, so users do not have to remember the IP address of the node

service nodeports are limited to high numbered ports that are greater than 30,000, to get around this you can deploy a proxy web-server 

the proxy will proxy the connection from port 80 to port 38080 

this example is for an on-prem deployment

for a public cloud environment, the CSP will automatically add a load balancer that will routr traffic from common ports to the cluster service ports, i.e., port 80 to the service port 38080

if you add another web service to the application that uses a separate load balancer, you will have to add another load balancer to map the web pages to a specific load balancer, i.e., `/mypage loadbalancer1 /video loadbalancer2`

kubernetes can handle all of these complexities and issue SSL certs using `ingress`

the kubernetes ingress-service is deployed in two steps, deployment tools and configuration 

- tools: nginx, haproxy, traefix, contour, istio, GCE (GCE and nginx are supported by the K8 project)
- configuration: are called `ingress resources`

NOTE: remember kubernetes does not include an ingress-controller by default 

an ingress-controller required the use of a deployment, service, configmap, and a service account for auth

setup rules to handle url to application routing 

imperative way of creating ingress controller in K8 version 1.12+

`kubectl create ingress <ingress-name> --rule="host/path=service:port"`

`kubectl create ingress ingress-test --rule="wear.my-online-store.com/wear*=wear-service:80"`

















<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## CertificationTips

**Certification Tips – Imperative Commands with Kubectl**

While you would be working mostly the declarative way – using definition files, imperative commands can help in getting one time tasks done quickly, as well as generate a definition template easily. This would help save considerable amount of time during your exams.

Before we begin, familiarize with the two options that can come in handy while working with the below commands:

`--dry-run`: By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the `--dry-run=client` option. This will not create the resource, instead, tell you whether the resource can be created and if your command is right.

`-o yaml`: This will output the resource definition in YAML format on screen.

Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.

POD
Create an NGINX Pod

`kubectl run nginx --image=nginx`

Generate POD Manifest YAML file (-o yaml). Don’t create it(–dry-run)

`kubectl run nginx --image=nginx --dry-run=client -o yaml`

Deployment
Create a deployment

`kubectl create deployment --image=nginx nginx`

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

Generate Deployment with 4 Replicas

`kubectl create deployment nginx --image=nginx --replicas=4`

You can also scale a deployment using the kubectl scale command.

`kubectl scale deployment nginx --replicas=4` 

Another way to do this is to save the YAML definition to a file and modify

`kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx-deployment.yaml`

You can then update the YAML file with the replicas or any other field before creating the deployment.

Service
Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379

`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml`

(This will automatically use the pod’s labels as selectors)

Or

`kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml` (This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

Create a Service named nginx of type NodePort to expose pod nginx’s port 80 on port 30080 on the nodes:

`kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml`

(This will automatically use the pod’s labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml`

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

Reference:
https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands

https://kubernetes.io/docs/reference/kubectl/conventions/