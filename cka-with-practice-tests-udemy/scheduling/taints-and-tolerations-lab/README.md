### 1. How many nodes exist on the system? Including the `controlplane` node

```bash
$ kubectl get nodes --no-headers | wc -l
```

### 2. Do any taints exist on `node01` node?

```bash
$ kubectl describe node node01 | grep Taint
```

### 3. Create a Taint on `node01` with key of `spray`, value of `mortein` and effect of `NoSchedule`

```bash
$ kubectl taint node node01 spray=mortein:NoSchedule
$ kubectl describe node node01 | grep Taint
```

### 4. Create a new Pod with the `nginx` image and Pod name as `mosquito`

```bash
# 1st Way
$ cat mosquito-definition.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: mosquito
spec:
  containers:
  - image: nginx
    name: mosquito

$ kubectl create -f mosquito-definition.yaml
```

```bash
# 2nd Way
$ kubectl run mosquito --image=nginx
```

### 5. What is the status of the Pod?

*Answer:* Pending state because Pod `mosquito` cannot tolerate taint `mortein`

```bash
$ kubectl get pods -o wide
```

### 6. Create another Pod named `bee` with the `nginx` image, which has a Toleration set to the taint `mortein`

```bash
$ cat bee-definition.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: spray
    value: mortein
    effect: NoSchedule
    operator: Equal
```

```bash
# Create the new Pod
$ kubectl create -f bee-definition.yaml
```

```bash
# Check Pod toleration description
$ kubectl describe pod bee | grep -A2 Tolerations
```

### 7. Notice the `bee` Pod was scheduled on node `node01` despite the Taint

```bash
$ kubectl get pods bee -o wide
```

### 8. Do you see any taints on `controlplane` node?

```bash
$ kubectl describe nodes controlplane | grep Taint
```

### 9. Remove the Taint on `controlplane`, which currently has the Taint effect of `NoSchedule`

```bash
$ kubectl taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-
```

### 10. What is the state of the Pod `mosquito` now and on which node is it running on?

```bash
$ kubectl get pods mosquito -o wide
NAME       READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
mosquito   1/1     Running   0          6m20s   10.244.0.4   controlplane   <none>           <none>
```
