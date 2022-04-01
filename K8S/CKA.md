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
- *kube-scheduler*: schedule resources from master to worker node. Bind pods to node.
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

##### Kube-API server

###### Case: Process of creating a pod

1. Authenticate User
2. Validate Request
3. Retrieve data
4. Update ETCD
5. Scheduler
6. Kubelet

Step 1-4 create pod without assigning node, scheduler identify the right node to deploy new pod, use API server to update information to ETCD, and pass to kubelet.

Kubelet create pod on the node and instruct container runtime engine to deploy application image. API-server update 

API server is the **only components** to directly talk to ETCD, kubeadm can save us from setting api-server(kube-api server as an pod), in other ways to create cluster, we need to configure it.

```shell
# check all running servers
ps -aux | grep kube-apiserver
```

##### Kube Controller Manager

Manage ships / containers on ships

1. Watch status
2. Remediate situation

- Node-controller
  - check status of node 5sec/time using kube-apiserver
  - unreachable pod: 5min to go back up: pod eviction Timeout,
  - not come back pod: provision with new pod
- Replicate-controllers
  - make sure the number of pods is constant

All controller is integrated in kube controller manager.

```shell
# download and customize controller, set up node, replicate controllers
# setup with kubeadm in master node:
kubectl get pods -n kube-system
# /menifests/kube-controller-manager.yaml

# setup from scratch
kube-controller-manager.server
ps -aux | grep kube-controller-manager
```

##### Kube-scheduler

Scheduler: *deciding* which pods go to which node, not place it. *Kubelet place it.*

###### How need scheduler

Right 