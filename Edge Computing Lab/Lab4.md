## Lab 4

### Architecture

1. Kubernetes cluster on server, send application from master machine to worker machines.
2. Edge server(Dell Power Edge XE8545, Cloud server - VM) + Edge node(Developer Kit).

Hide the details to protect copyright.

#### Task 5: Access the OpendataCam Web UI

```
NAME                TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                        AGE
kubernetes          ClusterIP      10.43.0.1       <none>          443/TCP                                        47h
opendatacam         LoadBalancer   10.43.127.12    192.168.85.67   8070:32541/TCP,8090:30191/TCP,8080:32588/TCP   3h38m
opendatacam-mongo   ClusterIP      10.43.184.199   <none>          27017/TCP                                      10m
```

Open: 192.168.85.67:32588, I can use the following command to check the logs.

```shell
kubectl logs -f <pod-name>
```

![1649056199129](../pics/1649056199129.png)

I set the counters at Lane Leftmost (red line), Lane Left1 (purple line), Lane Left (yellow line), Lane Middle (green line), Lane Right (blue line).

The counter result is as follow:

![1649056229685](../pics/1649056229685.png)