### Intro

In this lab, you will get hands-on practice with creating Kubernetes objects **imperatively**. All the questions in this lab can be done imperatively. However, for some questions, you may need to first create the YAML file using imperative methods. You can then modify the YAML according to the need and create the object using `kubectl apply -f` command.

### 1. Deploy a Pod named `nginx-pod` using the `nginx:alpine` image (only imperative commands are allowed)

`$ kubectl run nginx-pod --image=nginx:alpine`

### 2. Deploy a `redis` Pod using the  `redis:alpine` image with the labels set to `tier=db`

Either use imperative commands to create the pod with the labels. Or else use imperative commands to generate the pod definition file, then add the labels before creating the pod using the file.

```bash
# 1st Way -- Imperative command
$ kubectl run redis --image=redis:alpine --labels="tier=db"
```

```bash
# 2nd Way -- Via generating a definition file
$ kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis-pod.yaml
```

Add given labels `tier=db` under the `metadata` section

```bash
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
```

Then run the command:

`$ kubectl create -f redis-pod.yaml` to create the pod from the definition file`

### 3. Create a Service `redis-service` to expose the redis application within the cluster on `port 6379` (only imperative commands are allowed)

`$ kubectl expose pod redis --port=6379 --name redis-service`

### 4. Create a Deployment named `webapp` using the image `kodekloud/webapp-color` with 3 replicas (only imperative commands are allowed)

`$ kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3`

### 5. Create a new Pod called `custom-nginx` using the `nginx` image and expose it on container `port 8080`

`$ kubectl run custom-nginx --image=nginx --port=8080`

### 6. Create a new Namespace called `dev-ns` (only imperative commands are allowed)

```bash
# 1st Way
$ kubectl create namespace dev-ns

# 2nd Way
$ kubectl create ns dev-ns
```

### 7. Create a new Deployment called `redis-deploy` in the `dev-ns` Namespace with the `redis` image with 2 replicas (pnly imperative commands are allowed)

```bash
# 1st Way -- Imperative command
$ kubectl create deployment redis-deploy --image=redis --replicas=2 -n dev-ns
```

```bash
# 2nd Way -- Via generating a YAML file and applying it
$ kubectl create deployment redis-deploy --image=redis --namespace=dev-ns --replicas=2 --dry-run=client -o yaml > redis-deploy-definition.yaml
$ kubectl apply -f redis-deploy-definition.yaml
$ kubectl get deploy -n dev-ns
```

### 8. Create a Pod called `httpd` using the image `httpd:alpine` in the `default` Namespace. Next, create a service of type `ClusterIP` by the same name (httpd). The target port for the service should be 80 (try to do this with as few steps as possible)

```bash
$ kubectl run httpd --image=httpd:alpine --port=80 --expose
```
