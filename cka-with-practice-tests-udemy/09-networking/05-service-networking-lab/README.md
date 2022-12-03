### 1. What network range are the nodes in the cluster part of?

*Answer:* The network range the nodes are in is **`10.40.80.0/24`**.

One way to do this is to make use of the `ipcalc` utility. If it is not installed, you can install it by running:

```bash
apt update and the apt install ipcalc
```

Then use it to determine the network range as shown below:

First, find the Internal IP of the nodes:

```bash
$ ip addr | grep eth0 --color
$ kubectl get nodes -o wide
```

Next, use the `ipcalc` tool to see the network details:

```
$ ipcalc -b 10.40.80.0/24
```

### 2. What is the range of IP addresses configured for Pods on this cluster?

*Answer:* The network is configured with `weave`. After checking the `weave` Pod logs using below command and looking for `ipalloc-range`: 

```bash
$ cat /etc/cni/net.d/10-weave.conflist
$ kubectl get pods -n kube-system | grep weave
$ kubectl logs weave-net-rpn5n weave -n kube-system | grep -i ipalloc --color
$ kubectl logs weave-net-sgmc7 weave -n kube-system | grep -i ipalloc --color
$ kubectl describe pod weave-net-rpn5n -n kube-system | grep -i ipalloc
$ kubectl describe pod weave-net-sgmc7 -n kube-system | grep -i ipalloc
```

we can conclude that the IP range configured for the Pods in the cluster is **`10.50.0.0/16`**.

### 3. What is the IP Range configured for the services within the cluster?

*Answer:* After inspecting the setting on `kube-apiserver` by running the command: 

```bash
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep cluster-ip-range
```

we see that the IP range configured for the services in the cluster is **`10.96.0.0/12`**.

### 4. How many `kube-proxy` Pods are deployed in this cluster?

*Answer:* There are **2** `kube-proxy` Pods deployed in the cluster:

- `kube-proxy-2mcqn`
- `kube-proxy-ll5p5`

```bash
$ kubectl get pods -n kube-system -o wide | grep proxy
```

### 5. What type of proxy is the `kube-proxy` configured to use?

*Answer:* After checking ogs of the `kube-proxy` Pods: 

```bash
$ kubectl logs kube-proxy-2mcqn -n kube-system | grep -i proxymode
$ kubectl logs kube-proxy-ll5p5 -n kube-system | grep -i proxymode
```

### 6. How does this Kubernetes cluster ensure that a `kube-proxy` Pod runs on all nodes in the cluster?

*Hint:* Inspect the `kube-proxy` Pods and try to identify how they are deployed.

*Answer:* By using a **DaemonSet**:

```bash
$ kubectl get daemonsets -n kube-system -o wide
```
