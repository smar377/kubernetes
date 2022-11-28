### 1. We have a working kubernetes cluster with a set of applications running. Let us first explore the setup

How many deployments exist in the cluster?

*Answer:* There are **2** Deployments in the cluster, the `blue` and the `red`.

```bash
$ kubectl get deploy -o wide -n default
```

### 2. What is the version of ETCD running on the cluster?

*Hint:* Look at the ETCD Logs or check the image used by ETCD Pod.

*Answer:* The `etcd` version running is `3.5.3-0`

```bash
$ kubectl describe pod etcd-controlplane -n kube-system | grep -i image
```

### 3. At what address can you reach the ETCD cluster from the `controlplane` node?

*Hint:*  Check the ETCD Service configuration in the ETCD Pod and look for `--listen-client-urls`.

*Answer:* `https://127.0.0.1:2379`

```bash
$ kubectl describe pod etcd-controlplane -n kube-system
```

### 4. Where is the ETCD server certificate file located?

*Note* this path down as you will need to use it later.

*Answer:* The ETCD server certificate is located in `/etc/kubernetes/pki/etcd/server.crt`

```bash
$ kubectl -n kube-system describe pod etcd-controlplane | grep '\--cert-file'
```

### 5. Where is the ETCD CA Certificate file located?

*Note* this path down as you will need to use it later.

*Answer:* The ETCD CA certificate file is located in `/etc/kubernetes/pki/etcd/ca.crt`.

```bash
$ kubectl -n kube-system describe pod etcd-controlplane | grep '\--trusted-ca-file'
```

### 6. The master node in our cluster is planned for a regular maintenance reboot tonight

While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the ETCD database using the built-in snapshot functionality.

Store the backup file at location `/opt/snapshot-pre-boot.db`.

*Hint:* Use the `etcdctl snapshot save` command. You will have to make use of additional flags to connect to the ETCD server:

1. `--endpoints`: Optional Flag, points to the address where ETCD is running (127.0.0.1:2379)
2. `--cacert`: Mandatory Flag (Absolute Path to the CA certificate file)
3. `--cert`: Mandatory Flag (Absolute Path to the Server certificate file)
4. `--key`: Mandatory Flag (Absolute Path to the Key file)

*Answer:*

```bash
ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/snapshot-pre-boot.db
```

### 7.

*Hint:*

*Answer:*

```bash

```

### 8.

*Hint:*

*Answer:*

```bash

```

### 9.

*Hint:*

*Answer:*

```bash

```
