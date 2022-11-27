### 1. This lab tests your skills on upgrading a Kubernetes cluster. We have a production cluster with applications running on it. Let us explore the setup first

What is the current version of the cluster?

*Answer:* `1.24.0`

```bash
$ kubectl get nodes -o wide
```

### 2. How many nodes are part of this cluster (including controlplane and worker nodes)?

*Answer:* We have in total **2** nodes in the Kubernetes cluster.

```bash
$ kubectl get nodes -o wide
```

### 3. How many nodes can host workloads in this cluster (inspect the applications and taints set on the nodes)

*Answer:* Both `controlplane` and `node01` can host workloads in the cluster as there is no Taint configured on them.

```bash
$ kubectl describe nodes controlplane | grep -i taints
$ kubectl describe nodes node01 | grep -i taints
```

### 4. How many applications are hosted on the cluster?

*Hint:* Count the number of Deployments in the default namespace.

*Answer:* There is **1** Deployment currently hosted on the cluster.

```bash
$ kubectl get deploy -o wide -n default
```

### 5. What nodes are the pods hosted on?

*Answer:* On both `controlplane` and `node01` nodes.

```bash
$ kubectl get pods -o wide -n default
```

### 6. You are tasked to upgrade the cluster

Users accessing the applications must **NOT** be impacted, and you **CANNOT** provision new VMs.
What strategy would you use to upgrade the cluster?

*Answer:* Upgrade one node at a time, while moving the workloads to the other.

### 7. What is the latest stable version of Kubernetes as of today?

*Hint:* Look at the `remote version` in the output of the `kubeadm upgrade plan` command.

*Answer:* Version `1.25.4`.

```bash
$ kubeadm upgrade plan
```

### 8. What is the latest version available for an upgrade with the current version of the kubeadm tool installed?

*Hint:* Use the `kubeadm` tool.

*Answer:* Version `1.24.8`.

```bash
$ kubeadm upgrade plan
```

### 9. We will be upgrading the `controlplane` node first. Drain the `controlplane` node of workloads and mark it `UnSchedulable`

*Hint:* There are DaemonSets created in this cluster, especially in the `kube-system` namespace. To ignore these objects and drain the node, we can make use of the `--ignore-daemonsets` flag.

```bash
$ kubectl drain controlplane --ignore-daemonsets
```

### 10. Upgrade the `controlplane` components to exact `version v1.25.0`

*Hint:* Upgrade the `kubeadm` tool (if not already), then the `controlplane` components, and finally the `kubelet`. Practice referring to the Kubernetes documentation page.

*Note:* While upgrading `kubelet`, if you hit dependency issues while running the `apt-get upgrade kubelet` command, use the `apt install kubelet=1.25.0-00` command instead.

**Steps:**
1. Upgrade `kubeadm` tool to `version 1.25.0`
2. Check if `kubeadm` tool version is correct
3. Upgrade the `controlplane` node components
4. Upgrade `kubectl` and `kubelet`
5. Reload the `daemon` and restart `kubelet` service after it has been upgraded

```bash
# STEP 1
apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.25.0-00 && \
apt-mark hold kubeadm
```

```bash
# STEP 2
$ kubeadm version
```

```bash
# STEP 3
$ kubeadm upgrade apply v1.25.0
```

```bash
# STEP 4
apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.25.0-00 kubectl=1.25.0-00 && \
apt-mark hold kubelet kubectl
```

```bash
# STEP 5
$ systemctl daemon-reload
$ systemctl restart kubelet
```

### 11. Mark the `controlplane` node as `Schedulable` again

```bash
$ kubectl uncordon controlplane
$ kubectl get nodes -o wide
```

### 12. Next is the `worker` node. Drain the worker node of the workloads and mark it `UnSchedulable`

```bash
$ kubectl drain node01 --ignore-daemonsets
```

### 13. Upgrade the `worker` node to the exact `version v1.25.0`

*Important:* We need to SSH to `node01` in order to upgrade `kubeadm`, `kubectl` and `kubelet`!

**Steps:**
1. Upgrade `kubeadm` tool to `version 1.25.0`
2. Check if `kubeadm` tool version is correct
3. Upgrade the `node01` node configuration
4. Upgrade `kubectl` and `kubelet`
5. Reload the `daemon` and restart `kubelet` service after it has been upgraded

```bash
# STEP 1
apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.25.0-00 && \
apt-mark hold kubeadm
```

```bash
# STEP 2
$ kubeadm version
```

```bash
# STEP 3
$ kubeadm upgrade node
```

```bash
# STEP 4
apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.25.0-00 kubectl=1.25.0-00 && \
apt-mark hold kubelet kubectl
```

```bash
# STEP 5
$ systemctl daemon-reload
$ systemctl restart kubelet
```

### 14. Remove the restriction and mark the `worker` node as `Schedulable` again

```bash
$ kubectl uncordon node01
$ kubectl get nodes -o wide
```
