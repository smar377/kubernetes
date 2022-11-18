### 1. Create a Deployment from a file 

```bash
$ kubectl create -f <DEPLOYMENT_DEFINITION_NAME>.yaml
```

### 2. Create a named Deployment with specific number of replicas and images

```bash
$ kubectl create deployment <DEPLOYMENT_NAME> --image=<IMAGE_NAME> --replicas=<NUMBER>
```

### 3. Check the details of a Deployment

```bash
$ kubectl describe deployment <DEPLOYMENT_NAME>
```

### 4. Delete a Deployment

```bash
$ kubectl delete deployment <DEPLOYMENT_NAME>
```

### 5. Check the number of Deployments running on the system

```bash
$ kubectl get deployment -o wide
```
