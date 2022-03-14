### CKA Course

#### Introduction

##### Certification Details

Use the code - **DEVOPS15** - while registering for the CKA or CKAD exams at Linux Foundation to get a 15% discount.

##### The Kubernetes Trilogy

- For absolute beginners
  - prerequisites, services

- For administrators
  - Advances and indepth
  - High available
  - Maintenance 
  - Troubleshooting

- For developers
  - Basics on development in Python and NodeJS
  - Multi-container Pods

#### Core Concepts

##### Cluster Architecture

Master: manage, plan, schedule, monitor nodes

- *ETCD*: key-value database to store the configuration
- *kube-scheduler*: schedule resources from master to worker node
- *Controller-manager*
  - Node controller: on board new board to create and distroy
  - Replication controller: make sure the desired number of containers
- *API-server*: orchestrating all the components above, monitor the status of node, connect containers to server

Worker Nodes: host application and containers - one cargo to carry containers, one to control

For containers run application: container runtime on each node - *Docker*, containerd etc.

*kubelet:* captain for each ship. Each node on the cluster.

*kube-proxy services*: communication among worker nodes, e.g. communication between database server and web server in different containers.

##### ETCD

###### ETCD

Distributed reliable key-value store that is simple, secure and fast. Fast write and read the configuration, use etcdctl to set, query key-value.

###### Contents

Nodes, pods, configs, secrets, accounts, roles, bindings and others.

There are 2 ways to deploy k8s cluster.

1. From scratch to setup cluster, download, install and configure etcd manually.
2. Use kubeadm to setup cluster.

Advertise-client-url: kube-api server to etcd server.









