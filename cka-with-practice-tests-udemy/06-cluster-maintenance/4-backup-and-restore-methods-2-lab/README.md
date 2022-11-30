**Important**: In this lab environment, you will get to work with multiple kubernetes clusters where we will practice backing up and restoring the ETCD database.

You will notice that, you are logged in to the `student-node` (instead of the `controlplane`).

The `student-node` has the `kubectl` client and has access to all the Kubernetes clusters that are configured in this lab environment.

Before proceeding to the next question, explore the `student-node` and the clusters it has access to.

### 1. How many clusters are defined in the `kubeconfig` on the `student-node`?

*Hint:* You can make use of the `kubectl config` command.

*Answer:* You can view the complete `kubeconfig` by running:

```bash
$ kubectl config view
```

Alternatively, run the following command to just display the clusters:

```bash
$ kubectl config get-clusters
```

### 2. How many nodes (both `controlplane` and `worker`) are part of `cluster1`?

*Hint:* Make sure to switch the context to cluster1:

```bash
$ kubectl config use-context cluster1
```

*Answer:* There are **2** nodes in `cluster1`, `cluster1-controlplane` node and `cluster1-node01` node.

```bash
$ kubectl get nodes -o wide
```

### 3. What is the name of the `controlplane` node in `cluster2`?

*Answer:* The name of the `controlplane` node in `cluster2` is `cluster2-controlplane`.

```bash
# Switch to the context of cluster2
$ kubectl config use-context cluster2

# Check the nodes of cluster2
$ kubectl get nodes -o wide
```

### 4. How is ETCD configured for `cluster1`?

*Hint:* Remember, you can access the clusters from `student-node` using the `kubectl` tool. You can also SSH to the cluster nodes from the `student-node`.

*Answer:* If you check out the Pods running in the `kube-system` namespace in `cluster1`, you will notice that etcd is running as a Pod:

```bash
# Make sure to switch the context to cluster1
$ kubectl config use-context cluster1

$ kubectl get pods -n kube-system | grep etcd
```

This means that ETCD is set up as a **Stacked ETCD Topology** where the distributed data storage cluster provided by etcd is stacked on top of the cluster formed by the nodes managed by kubeadm that run control plane components.

### 5. How is ETCD configured for `cluster2`?

*Hint:* Remember, you can access the clusters from `student-node` using the `kubectl` tool. You can also SSH to the cluster nodes from the `student-node`.

*Answer:* External ETCD

```bash
# Make sure to switch the context to cluster2
$ kubectl config use-context cluster2
```

If you check out the pods running in the `kube-system` namespace in `cluster1`, you will notice that there are **NO** `etcd` Pods running in this cluster:

```bash
$ kubectl get pods -n kube-system  | grep etcd
```

Also, there is **NO** static pod configuration for `etcd` under the static Pod path:

```bash
$ ls -lah /etc/kubernetes/manifests/ | grep -i etcd
```

However, if you inspect the process on the `controlplane` for `cluster2`, you will see that that the process for the `kube-apiserver` is referencing an external `etcd` datastore:

```bash
$ ps -ef | grep etcd
```

You can see the same information by inspecting the `kube-apiserver` Pod (which runs as a static pod in the `kube-system` namespace):

```bash
$ kubectl -n kube-system describe pod kube-apiserver-cluster2-controlplane
```

### 6. What is the IP address of the External ETCD datastore used in `cluster2`?

*Answer:* The IP address of the External ETCD datastore used in `cluster2` is `10.30.239.23`

```bash
$ kubectl -n kube-system describe pod kube-apiserver-cluster2-controlplane | grep '\--etcd-servers'
```

### 7. What is the default data directory used the for ETCD datastore used in `cluster1`?

*Hint:* Remember, this cluster uses a Stacked ETCD topology.

*Answer:* `/var/lib/etcd`

```bash
# Make sure to switch the context to cluster1
$ kubectl config use-context cluster1

# Inspect the value assigned to data-dir on the etcd Pod
$ kubectl describe pod etcd-cluster1-controlplane -n kube-system | grep '\--data-dir'
```

**Important:** For the subsequent questions, you would need to login to the External ETCD server

To do this, open a new terminal (using the `"+"` button located above the default terminal).

From the new terminal you can now SSH from the `student-node` to either the IP of the ETCD datastore (that you identified in the previous questions) OR the hostname `etcd-server`:

### 8. What is the default data directory used the for ETCD datastore used in `cluster2`?

*Hint:* Remember, this cluster uses an External ETCD topology.

*Answer:* The default data directory used the for ETCD datastore used in `cluster2` is `/var/lib/etcd-data`.

```bash
# Make sure to switch the context to cluster2
$ kubectl config use-context cluster2

# Login to the external ETCD datastore using the IP identified previously
$ ssh etcd-server
OR
$ ssh 10.30.239.23

# Check the running etcd process
$ ps -ef | grep etcd
```

### 9. How many nodes are part of the ETCD cluster that `etcd-server` is a part of?

*Answer:* Only **1** node is part of the ETCD cluster that `etcd-server` is part of.

```bash
# Export environmental variable ETCDCTL
etcd-server $ export ETCDCTL_API=3
etcd-server $ etcdctl version
etcdctl version: 3.4.20
API version: 3.4
```

Check the members of the cluster:

```bash
etcdctl \
 --endpoints=https://127.0.0.1:2379 \
 --cacert=/etc/etcd/pki/ca.pem \
 --cert=/etc/etcd/pki/etcd.pem \
 --key=/etc/etcd/pki/etcd-key.pem \
  member list
```

### 10. Take a backup of `etcd` on `cluster`1 and save it on the `student-node` at the path `/opt/cluster1.db`

*Hint:* If needed, make sure to set the context to `cluster1` (on the `student-node`).

Make sure to switch the context to `cluster1`:

```bash
$ kubectl config use-context cluster1
```
Next, inspect the endpoints and certificates used by the `etcd` Pod. We will make use of these to take the backup:

```bash
$ kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep advertise-client-urls
$ kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep pki
```

SSH to the `controlplane` node of `cluster1` and then take the backup using the endpoints and certificates we identified above:

```bash
$ ssh cluster1-controlplane
```

```bash
ETCDCTL_API=3 etcdctl --endpoints=https://10.30.239.9:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/cluster1.db
```

Finally, copy the backup to the `student-node`. To do this, go back to the `student-node` and use `scp` as shown below:

```bash
$ scp cluster1-controlplane:/opt/cluster1.db /opt
```

### 11. An ETCD backup for `cluster2` is stored at `/opt/cluster2.db`. Use this snapshot file to carry out a restore on `cluster2` to a new path `/var/lib/etcd-data-new`

Once the restore is complete, ensure that the `controlplane` components on `cluster2` are running.

The snapshot was taken when there were objects created in the `critical` namespace on `cluster2`. These objects should be available post restore.

*Hint:* If needed, make sure to set the context to cluster2 (on the student-node):

```bash
$ kubectl config use-context cluster2
```

**Step 1:** Copy the snapshot file from the `student-node` to the `etcd-server`. In the example below, we are copying it to the `/root` directory:

```bash
scp /opt/cluster2.db etcd-server:/root
```

**Step 2:** Restore the snapshot on `cluster2`. Since we are restoring directly on the `etcd-server`, we can use the `endpoint https:/127.0.0.1`. Use the same certificates that were identified earlier. Make sure to use the `data-dir` as `/var/lib/etcd-data-new`: 

```bash
# SSH to etcd-server of cluster2
$ ssh etcd-server
```

```bash
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
--cacert=/etc/etcd/pki/ca.pem \
--cert=/etc/etcd/pki/etcd.pem \
--key=/etc/etcd/pki/etcd-key.pem \
snapshot restore /root/cluster2.db --data-dir /var/lib/etcd-data-new
```

**Step 3:** Update the `systemd service` unit file for `etcd` by running `vi /etc/systemd/system/etcd.service` and add the new value for `data-dir`:

```bash
$ vi /etc/systemd/system/etcd.service
```

**Step 4:** Make sure the permissions on the new directory is correct (should be owned by `etcd` user):

```bash
# Check ownership
$ ls -lah /var/lib/etcd-data-new/

# Change ownership and check again
$ chown -R etcd:etcd /var/lib/etcd-data-new
$ ls -lah /var/lib/etcd-data-new/
```

**Step 5:** Finally, reload and restart the `etcd` service.

```bash
$ systemctl daemon-reload 
$ systemctl restart etcd
```

**Step 6 (optional):** It is recommended to restart `controlplane` components (e.g. `kube-scheduler`, `kube-controller-manager`, `kubelet`) to ensure that they don't rely on some stale data.
