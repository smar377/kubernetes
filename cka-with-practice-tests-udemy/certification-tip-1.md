## POD

### 1. Create an NGINX Pod

```bash
$ kubectl run nginx --image=nginx
```

### 2. Generate POD Manifest YAML file (-o yaml). Don't create it (--dry-run)

```bash
$ kubectl run nginx --image=nginx --dry-run=client -o yaml
```

## DEPLOYMENT

### 1. Create a Deployment

```bash
$ kubectl create deployment --image=nginx nginx
```

### 2. Generate Deployment YAML file `(-o yaml)`. Don't create it `(--dry-run)`

```bash
$ kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
```

### 3. Generate Deployment with 4 Replicas

```bash
$ kubectl create deployment nginx --image=nginx --replicas=4
```

You can also scale a Deployment using the kubectl `scale` command:

```bash
$ kubectl scale deployment nginx --replicas=4
```

### 4. Another way to do this is to save the YAML definition to a file and modify

```bash
$ kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx-deployment.yaml
```

You can then update the YAML file with the replicas or any other field before creating the deployment.

## SERVICE

### 1. Create a Service named `redis-service` of type ClusterIP to expose pod redis on `port 6379`

```bash
$ kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```

This will automatically use the pod's labels as selectors.

*OR*

```bash
$ kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
```

This will not use the pods labels as selectors, instead it will assume selectors as `app=redis`. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service.

### 2. Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes

```bash
kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml
```

This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.

*OR*

```bash
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml
```

This will not use the pods labels as selectors.

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

*References:*
- [kubectl-commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
- [kubectl-usage-conventions](https://kubernetes.io/docs/reference/kubectl/conventions/)
