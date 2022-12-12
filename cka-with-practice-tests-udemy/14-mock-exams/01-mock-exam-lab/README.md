### 1. Deploy a Pod named `nginx-pod` using the `nginx:alpine` image

*Once done, click on the Next Question button in the top right corner of this panel. You may navigate back and forth freely between all questions. Once done with all questions, click on End Exam. Your work will be validated at the end and score shown. Good Luck!*

*Answer:* We will use the following command:

```bash
$ kubectl run nginx-pod --image=nginx:alpine
```

### 2. Deploy a `messaging` Pod using the `redis:alpine` image with the labels set to `tier=msg`

*Answer:* We will use the following command:

```bash
$ kubectl run messaging --image=redis:alpine --labels='tier=msg'
```

### 3. Create a namespace named `apx-x9984574`

*Answer:* We will use the following command:

```bash
$ kubectl create namespace apx-x9984574
```

### 4. Get the list of nodes in JSON format and store it in a file at `/opt/outputs/nodes-z3444kd9.json`

*Answer:* We will use the following command:

```bash
$ kubectl get nodes -o json > /opt/outputs/nodes-z3444kd9.json
```

### 5. Create a Service `messaging-service` to expose the `messaging` application within the cluster on `port 6379`

*Hint:* Use imperative commands.

*Answer:* We will use the following command:

```bash
$ kubectl expose pod messaging --port=6379 --name=messaging-service --type=ClusterIP
$ kubectl get svc -o wide
```

### 6. Create a Deployment named `hr-web-app` using the image `kodekloud/webapp-color` with 2 replicas

*Answer:* We will use the following command:

```bash
$ kubectl create deployment hr-web-app --image=kodekloud/webapp-color --replicas=2
$ kubectl get pod,svc,deploy -o wide
```

### 7. Create a static Pod named `static-busybox` on the `controlplane` node that uses the `busybox` image and the command sleep 1000

*Hint:*

*Answer:*

```bash
$ vi /etc/kubernetes/manifests/static-busybox-pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-busybox
spec:
  containers:
    - name: static-busybox
      image: busybox
      command: ["sleep", "1000"]
```

Verification:

```bash
$ kubectl describe pod static-busybox-controlplane
```

### 8. Create a Pod in the `finance` namespace named `temp-bus` with the image `redis:alpine`

*Answer:*

```bash
# Check if namespace finance exists
$ kubectl get namespaces | grep finance

# Create the requested Pod
$ kubectl run temp-bus --image=redis:alpine --namespace=finance --restart=Never
```

### 9. A new application `orange` is deployed. There is something wrong with it. Identify and fix the issue

*Answer:*

```bash
# Check in which namespace is the orange app deployed
$ kubectl get pod -A | grep orange

# Check description to get a first overview of what might be wrong with it
$ kubectl describe pod orange
```

From the description we can see that the issue probably lies in one of the `init containers` included in the Pod, named `init-myservice`.
The issue here seems to be the fact that we try pass a command which is **NOT** valid:

```bash
$ kubectl describe pod orange | egrep -i -A10 'init-myservice:' | grep -i -A3 command
```

We need to fix that:

```bash
# Edit Pod and fix the issue
$ kubectl edit pod orange

# Recreate the Pod with correct command this time
$ kubectl replace --force -f /tmp/kubectl-edit-1807033266.yaml

# Check if Pod now is running as expected
$ kubectl get pod orange -o wide
$ kubectl describe pod orange
```

### 10. Expose the `hr-web-app` as Service `hr-web-app-service` application on `port 30082` on the nodes on the cluster

*Hint:* The web application listens on `port 8080`.

*Answer:*

```bash
# Overview of all Kubernetes objects we will work with
$ kubectl get pod,svc,deploy -o wide

# Create the requested YAML Service definition file
$ kubectl expose deployment hr-web-app --type=NodePort --port=8080 --name=hr-web-app-service --dry-run=client -o yaml > hr-web-app-service.yaml
```

Now, in the generated service definition file add the `nodePort` field with the given port number under the `ports section` and create the service issuing the command:

```bash
$ kubectl create -f hr-web-app-service.yaml
```

### 11. Use JSON PATH query to retrieve the osImages of all the nodes and store it in a file /opt/outputs/nodes_os_x43kj56.txt.

*Hint:* The `osImages` are under the `nodeInfo` section under `status` of each node.

*Answer:*

```bash

```

### 12. 

*Hint:*

*Answer:*

```bash

```
