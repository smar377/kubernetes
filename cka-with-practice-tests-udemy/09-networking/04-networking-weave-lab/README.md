### 1. How many Nodes are part of this cluster (including master and worker nodes)?

*Answer:* There are **2** nodes in the cluster, namely `controlplane` and `node01` nodes.

```bash
$ kubectl get nodes
```

### 2. What is the networking solution used by this cluster?

*Answer:* After checking the config file located at `/etc/cni/net.d/10-weave.conflist` we can conclude that the Weave networking solution is in use:

```bash
$ ls -lah /etc/cni/net.d/10-weave.conflist
```

### 3. How many `weave` agents/peers are deployed in this cluster?

*Hint:*

*Answer:* After running below commad we can see that there are **2** `weave` agents/peers deployed in the cluster:

```bash
$ kubectl get pods -n kube-system | grep weave 
```

### 4. On which nodes are the weave peers present?

*Answer:* One on every node by default and as per CNI standard. We can check also with:

```bash
$ kubectl get pods -n kube-system -o wide
```

### 5. Identify the name of the bridge network/interface created by `weave` on each node.

*Answer:* The name of the bridge network/interface created by `weave` on each node is called `weave`.

```bash
$ ip link
```

### 6. What is the Pod IP address range configured by `weave`?

*Answer:* The Pod IP address range configured by `weave` is **`10.x.x.x`** (specifically **`10.50.0.0/16`**):

```bash
$ ip addr show weave
$ kubectl get pods -n kube-system
$ kubectl logs -n kube-system weave-net-bbhb2 weave
```

### 7. What is the default gateway configured on the Pods scheduled on `node01`?

*Hint:* Try scheduling a Pod on `node01` and check `ip route` output.

*Answer:* SSH to `node01`, then run below command and look at the `weave` line:

```bash
$ ip route
```

We will also do what is being suggested, meaning try scheduling a Pod on `node01` and check `ip route` output:

```bash
$ kubectl run busybox --image=busybox --dry-run=client -o yaml -- sleep 1000 > busybox-pod.yaml
```

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  nodeName: node01
  containers:
  - args:
    - sleep
    - "1000"
    image: busybox
    name: busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```bash
$ kubectl create -f busybox-pod.yaml
```

Verification:

```bash
$ kubectl get pod -o wide
$ kubectl exec -it busybox -- ip route
```
So based on above, we can conclude that the default gateway configured on the Pods scheduled on `node01` is **`10.50.192.0`**.
