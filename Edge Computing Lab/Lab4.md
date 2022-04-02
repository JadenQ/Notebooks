### Architecture

1. Kubernetes cluster on server, send application from master machine to worker machines.
2. Edge server(Dell Power Edge XE8545, Cloud server - VM) + Edge node(Developer Kit).

![1648879905018](D:\document\CUHK\Notebooks\pics\1648879905018.png)

### Use the VM

This server is where K8s cluster located.

```shell
sudo useradd s1155161048
sudo passwd s1155161048
sudo usermod -aG sudo s1155161048
```

#### Task 1: Install lightweight K8s to Xavier NX

Step1, Install k3s to NX, NX will become both the master and worker node

```shell
sudo chmod 777 /etc/rancher/k3s/k3s.yaml
kubectl get nodes -o wide
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube
kubectl get pod -n kube-system  traefik-56c4b88c4b-xkzcr -o jsonpath='{.metadata.uid}'

# get the pod id
kubectl get pods -n <namespace> -o custom-columns=PodName:.metadata.name,PodUID:.metadata.uid
kubectl get pods -n kube-system -o custom-columns=PodName:.metadata.name,PodUID:.metadata.uid

kubectl exec -it 957984f5-5b8e-44f0-a6f3-94f2ef5583b4 -- /bin/bash --namespace kube-system
kubectl exec -it pods/traefik-56c4b88c4b-xkzcr  -- /bin/bash 
```

#### Task 3: Deploy Opendatacam Pod

##### Step1 

Create folder and save config.json, configuration of opendatacam for YOLOv4.

```shell 
kubectl create configmap opendatacam --from-file=config.json --dry-run -o yaml | kubectl apply -f -
```

##### Step2

Configure opendatacam-deployment and opendatacam-service YAML files.

##### Step3

Deploy mongoDB

