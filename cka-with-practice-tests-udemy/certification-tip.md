### 1. Create an NGINX Pod

```bash
$ kubectl run nginx --image=nginx
```

### 2. Generate POD Manifest YAML file (-o yaml). Don't create it (--dry-run)

```bash
$ kubectl run nginx --image=nginx --dry-run=client -o yaml
```

### 3. Create a deployment

```bash
$ kubectl create deployment --image=nginx nginx
```

### 4. Generate Deployment YAML file (-o yaml). Don't create it (--dry-run)

```bash
$ kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
```

### 5. Generate Deployment YAML file (-o yaml). Don't create it (--dry-run) with 4 Replicas (--replicas=4)

```bash
$ kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
```

### 6. Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment

```bash
$ kubectl create -f nginx-deployment.yaml
```

*OR*

### 7. In k8s version 1.19+, we can specify the "--replicas" option to create a Deployment with 4 replicas

```bash
$ kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml
```
