### 1. How many nodes are part of this cluster (including the `controlplane` and `worker` nodes)?

*Answer:* There are **2** nodes in this cluster, namely `controlplane` and `node01` nodes.

```bash
$ kubectl get nodes -A
```

### 2. What is the internal IP address of the `controlplane` node in this cluster?

*Answer:* The internal IP of the `controlplance` node in the cluster is `10.37.211.3`.

```bash
$ kubectl get nodes -A -o wide
```

### 3. What is the network interface configured for cluster connectivity on the `controlplane` node (node-to-node communication)?

*Answer:* The network interface configured is `eth0`.

```bash
$ ip link
OR
$ ip addr
```

### 4. What is the MAC address of the interface on the `controlplane` node?

*Answer:* The MAC address of the interface on the `controlplane` node is `02:42:0a:25:d3:03`.

```bash
$ ip link show eth0
```

### 5. What is the IP address assigned to `node01`?

*Answer:* The internal IP of the `node01` node in the cluster is `10.37.211.6`.

```bash
$ kubectl get nodes -o wide
```

### 6. What is the MAC address assigned to `node01`?

*Answer:* SSH to the `node01` node and run below command:

```bash
$ ip link show eth0
```

### 7. We use `Containerd` as our container runtime. What is the interface/bridge created by `Containerd` on this host?

*Answer:* The interface/bridge created by `Containerd` on this host is `cni0`.

```bash
$ ip link
```

### 8. What is the state of the interface `cni0`?

*Answer:* The state of the interface `cni0` is `UP`.

```bash
$ ip link show cni0
```

### 9. If you were to ping google from the `controlplane` node, which route does it take? What is the IP address of the Default Gateway?

*Answer:* The IP address of the Default Gateway is `172.25.1.1`.

```bash
$ ip route show default
```

### 10. What is the port the `kube-scheduler` is listening on in the `controlplane` node?

*Answer:* The port the `kube-scheduler` is listening on in the `controlplane` node is `10259`.

```bash
$ netstat -tupln | grep kube-scheduler
```

### 11. Notice that ETCD is listening on two ports. Which of these have more client connections established?

*Answer:* The ETCD port with the most client connection established is `2379`.

***Correct!** That's because `2379` is the port of ETCD to which all control plane components connect to. `2380` is only for `etcd` peer-to-peer connectivity. When you have multiple controlplane nodes. In this case we don't.*

```bash
$ netstat -anp | grep etcd
```
