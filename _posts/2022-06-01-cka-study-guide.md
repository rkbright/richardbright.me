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


Helpful pre-requisite skills:

- Docker
- YAML

# Certification Objectives

- [Core concepts](#lesson1) 
  
  - Cluster architecture 
  - API primitives
  - Services and other network primitives 

- Scheduling 

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
  - **kube-proxy** runs on the worker nodes and ensures the rules ar ein place so containers can communicate 



