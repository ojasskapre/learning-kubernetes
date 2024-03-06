# Kubernetes For Beginners

Used for container orchestration. Container Orchestration is a process of automatically deploying and managing containers

Kubernetes has Master Nodes and Worker Nodes. There are various components in each node

Master Node

- API Server
- etcd
- Controller
- Scheduler

Worker Node

- kubelet
- kubeproxy
- container runtime

### Pods

- Smallest unit of Kubernetes cluster.
- Can host single or multiple containers.
- Generally only 1 container is hosted
- Multi container pod is used if we want an helper container to support the main container

There are various ways to create a Pod:

```bash
# kubectl run <pod-name> --image=<image-name>
kubectl run nginx --image=nginx

# get list of all pods
kubectl get pods

# get for information when listing all pods
kubectl get pods -o wide

# get description of specific pod - kubectl describe pod <pod-name>
kubectl describe pod nginx

# change image of the pod - kubectl set image pod/<pod-name> <pod-name>=<image-name>
kubectl set image pod/nginx nginx=nginx1.18

# get yaml configuration of pod - kubectl run <pod-name> --image=<image-name> --dry-run=client -o yaml
kubectl run nginx --image=nginx --dry-run=client -o yaml

# edit image of the pod by editing yaml in default kubectl editor - kubectl edit pod <pod-name>
kubectl edit pod nginx

# delete a pod - kubectl delete pod <pod-name>
kubectl delete pod nginx
```

Other way to create a Pod is using the yaml/yml configration file

A yaml configuration file for any resources in kuberentes will have the following structure

```yaml
apiVersion:
kind:
metadata:
spec:
```

```bash
# To apply a config file
kubectl apply -f <filename.yaml>

# To delete the configurations
kubectl delete -f <filename.yaml>
```

```bash
# to get the apiVersion for the resource kind - kubectl explain <kind>
kubectl explain pods
kubectl explain service
kubectl explain replicaset
kubectl explain deployment
```

Here's how we can define Pod's configuration file

```yaml
# pod-definition.yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    tier: frontend
spec:
  containers:
    - name: nginx
      image: nginx
```

```bash
# deploy the pod
kubectl apply -f pod-definition.yml

# change image without changing actual file - kubectl set image -f <config-file-name.yml> <pod-name>=<image-name>
kubectl set image -f pod-definition.yml nginx=nginx:1.18

# delete the pod
kubectl delete -f pod-definition.yml
```

### ReplicaSet

ReplicaSet is newer version of ReplicaController. It controls and manages deployment and scaling of the pods.

```yaml
# replicaset-definition.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx-app
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-pod
      tier: frontend
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx-pod
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx
```

```bash
# deploy the replicaset config
kubectl apply -f replicaset-definition.yml

# get list of replicasets
kubectl get replicaset

# get detailed information of a replicaset - kubectl describe replicaset <replicaset-name>
kubectl describe replicaset nginx-replicaset

# edit number of replicas without updating config file - kubectl edit replicaset <replicset-name>
kubectl edit replicaset nginx-replicaset

# scale number of replicas
# using type: name format - kubectl scale replicaset <replicaset-name> --replicas=<number-of-replicas>
# passing config file - kubectl scale -f <config-file-name.yml> --replicas-<number-of-replicas>
kubectl scale replicaset nginx-replicaset --replicas=5
kubectl scale -f replicaset-definition.yml --replicas=3
```

### Deployments

Deployments manages pods, rollout updates, rollback updates, autoscaling, and performs self-healing. It uses ReplicaSet under the hood for scaling and deploying pods

```yaml
# deployment-definition.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx-depl
    tier: frontend
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
  selector:
    matchLabels:
      app: nginx-pod
      tier: frontend
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx-pod
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx
```

```bash
# apply the deployment config
kubectl apply -f deployment-definition.yml --record

# get all deployments
kubectl get deployment

# describe a deployment - kubectl describe deployment <deployment-name>
kubectl describe deployment nginx-deployment

# change image without changing the config file
# kubectl set image deployment/<deployment-name> <pod-name>:<image-name>
kubectl set image deployment/nginx-deployment nginx:nginx:1.18

# check rollout status - kubectl rollout status deployment/<deployment-name>
kubectl rollout status deployment/nginx-deployment

# check rollout history - kubectl rollout history deployment/<deployment-name>
kubectl rollout history deployment/nginx-deployment

# rollback, undo rollout - kubectl rollout undo deployment/<deployment-name>
kubectl rollout undo deployment/nginx-deployment

# rollback to particular revision
# kubectl rollout undo deployment/<deployment-name> --to-revision=<revision-number>
kubectl rollout undo deployment/nginx-deployment --to-revision=1

# scale replicas - kubectl scale deployment <deployment-name> --replicas=<number-of-replicas>
kubectl scale deployment nginx-deployment --replicas=5

# delete all resources created using the config file
kubectl delete -f deployment-definition.yml
```

### Services

Enables communication between pods or pods and external applications.

Three types

1. NodePort: Exposes the pods to external clients using a static port
2. ClusterIP: Exposes the service to cluster internal IP address
3. LoadBalancer: Exposes the service using a Cloud Load Balancer

```yaml
# nodeport-service-definition.yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    app: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx-pods
    tier: frontend
  ports:
    - nodePort: 80
      port: 80
      targetPort: 30004
```

Locally, LoadBalancer will behave same as a NodePort. LoadBalancer uses the cloud native load balancer which helps to access the application from fixed endpoint urls. For example, if we have 4 nodes with 3 pods each. 2 nodes host application A and other nodes host application B. The pods are exposed using NodePort service on nodePort 30001 and 30002. Let's assume, the node ip address is 192.168.1.1, 192.168.1.2, 192.168.1.3, 192.168.1.4. Now, we can access application through each ip address port combination

Application A can be access using `http://192.168.1.1:30001`, `http://192.168.1.2:30001`, `http://192.168.1.3:30001`, `http://192.168.1.4:30001`. It can be accessed from the ip address which does not even host the application. Same for application B.

Customers wants to access the application using simple common urls like `http://application-A.com` or `http://application-B.com`. This can be achieved when we front our application using LoadBalancer.

```yaml
# loadbalancer-service-definition.yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    app: nginx-service
spec:
  type: LoadBalancer
  selector:
    app: nginx-pods
    tier: frontend
  ports:
    - nodePort: 80
      port: 80
      targetPort: 30004
```

```yaml
# cluster-ip-service-definition.yml - default type of Service is ClusterIp
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    app: nginx-service
spec:
  selector:
    app: nginx-pods
    tier: frontend
  ports:
    - port: 80
      targetPort: 30004
```
