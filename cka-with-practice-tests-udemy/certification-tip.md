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

### 1. Create a deployment

```bash
$ kubectl create deployment --image=nginx nginx
```

### 2. Generate Deployment YAML file (-o yaml). Don't create it (--dry-run)

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

### 1. Create a Service named `redis-service` of type ClusterIP to expose pod redis on port 6379

```bash
$ kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```
