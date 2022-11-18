### Intro

In this lab, you will get hands-on practice with creating Kubernetes objects imperatively. All the questions in this lab can be done imperatively. However, for some questions, you may need to first create the YAML file using imperative methods. You can then modify the YAML according to the need and create the object using `kubectl apply -f` command.

### 1. Deploy a Pod named `nginx-pod` using the `nginx:alpine` image (only imperative commands are allowed)

```bash
$ kubectl run nginx-pod --image=nginx:alpine
```

### 2. Deploy a `redis` Pod using the  `redis:alpine` image with the labels set to `tier=db`.

Either use imperative commands to create the pod with the labels. Or else use imperative commands to generate the pod definition file, then add the labels before creating the pod using the file.

```bash
# 1st Way -- Imperative command
$ kubectl run redis --image=redis:alpine -l tier=db
```

```bash
# 2nd Way -- Via generating a definition file
$ kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis-pod.yaml

# Add given labels `tier=db` under the `metadata` section
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    tier: db
  name: redis
spec:
  containers:
  - image: redis:alpine
    name: redis
  dnsPolicy: ClusterFirst
  restartPolicy: Always

# Then run the command 
$ kubectl create -f redis-pod.yaml` to create the pod from the definition file
```
