### 1. How to check the available nodes in a Kubernetes environment

```bash
$ kubectl get nodes
```

### 2. A Pod definition file `nginx.yaml` is given. Create a Pod using the file

```bash
$ kubectl create -f nginx.yaml
```

### 3. What is the status of the created Pod?

```bash
$ kubectl get pods nginx -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
nginx   0/1     Pending   0          54s   <none>   <none>   <none>           <none>
```

### 4. Why is the Pop in a pending state? Inspect the environment for various kubernetes control plane components

**Answer:** No scheduler present!

```bash
$ kubectl describe pod nginx 
Name:         nginx
Namespace:    default
Priority:     0
Node:         <none>
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           
IPs:          <none>
Containers:
  nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-7f6s6 (ro)
Volumes:
  kube-api-access-7f6s6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
```

Additionally, we can check if there is a `scheduler` container running on the Kubernetes system, by running the following command:

```bash
$ kubectl get pods -n kube-system
```

### 5. Manually schedule the Pod on `node01`. Delete and recreate the Pod if necessary.

```bash
# Delete the pod
$ kubectl delete pod nginx
```

```bash
# Edit YAML definition to add "nodeName" field for manual scheduling
$ cat nginx.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx
```

```bash
# Create the pod again
$ kubectl create -f nginx.yaml
```

```bash
# Check the pod status
$ kubectl get pods nginx -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE     NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          8s    10.244.1.2   node01   <none>           <none>
```

**IMPORTANT:** Another way to recreate the Pod by simply issuing only one command is:

```bash
$ kubectl replace --force -f nginx.yaml
```

Last, we can live watch the state of the Pod by issuing:

```bash
$ kubectl get pods --watch
```

### 6. Now schedule the same Pod on the `controlplane` node. Delete and recreate the Pod if necessary.

*Note*: We will use directly the second method we learned above, meaning the `replace` command.

```bash
# Edit YAML definition to add "nodeName" field for manual scheduling
$ cat nginx.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: controlplane
  containers:
  -  image: nginx
     name: nginx
```

```bash
# Recreate the pod
$ kubectl replace --force -f nginx.yaml
```

```bash
# Live check of pod status
$ kubectl get pods nginx -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          12s   10.244.0.4   controlplane   <none>           <none>
```
