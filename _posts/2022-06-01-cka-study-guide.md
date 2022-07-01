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

- Logging and maintenance 

  - Monitor cluster components 
  - Monitor applications 
  - Monitor cluster component logs 
  - Application logs

- Application lifecycle manager

  - Rolling updates and rollbacks in deployments 
  - Configure applications 
  - Scale applications 
  - Self-healing application 

- Cluster maintenance 

  - Cluster upgrade process
  - Operating system upgrades 
  - backup and restore methodologies 

- Security 

  - Authentication and authorization 
  - TLS certificates for cluster components 
  - Kubernetes security 
  - Images securely 
  - Network policies 
  - Security context 
  - Secure persistent key value store 

- Storage 

  - Persistent volumes 
  - Persistent volume claims 
  - Configure applications with persistent storage 
  - Access modes for volumes 
  - Kubernetes storage object 

- Networking 

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