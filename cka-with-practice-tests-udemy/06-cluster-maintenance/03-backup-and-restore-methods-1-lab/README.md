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

### 7. Wake up! We have a conference call! After the reboot the master nodes came back online, but none of our applications are accessible. Check the status of the applications on the cluster. What's wrong?

*Answer:* All Pods, Deployments and Services we had in the cluster are gone.

```bash
$ kubectl get pods,deploy,services -n default -o wide
```

### 8. Luckily we took a backup. Restore the original state of the cluster using the backup file

*Note:* In this case, we are restoring the snapshot to a different directory but in the same server where we took the backup (the `controlplane` node). As a result, the only required option for the restore command is the `--data-dir`.

```bash
ETCDCTL_API=3 etcdctl --data-dir /var/lib/etcd-from-backup \
snapshot restore /opt/snapshot-pre-boot.db
```

Next, update the `/etc/kubernetes/manifests/etcd.yaml`:

We have now restored the `etcd` snapshot to a new path on the `controlplane` - `/var/lib/etcd-from-backup`, so, the only change to be made in the YAML file, is to change the `hostPath` for the volume called `etcd-data` from old directory (`/var/lib/etcd`) to the new directory (`/var/lib/etcd-from-backup`).

```bash
volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
```

With this change, `/var/lib/etcd` on the container points to `/var/lib/etcd-from-backup` on the `controlplane` (which is what we want).

When this file is updated, the ETCD Pod is automatically re-created as this is a static pod placed under the `/etc/kubernetes/manifests` directory.

- *Note 1:* As the ETCD pod has changed it will automatically restart, and also `kube-controller-manager` and `kube-scheduler`. Wait 1-2 to mins for this Pods to restart. You can run the command: `watch "crictl ps | grep etcd"` to see when the ETCD Pod is restarted.

- *Note 2:* If the `etcd` Pod is not getting Ready 1/1, then restart it by `kubectl delete pod -n kube-system etcd-controlplane` and wait 1 minute.

- *Note 3:* This is the simplest way to make sure that ETCD uses the restored data after the ETCD Pod is recreated. You don't have to change anything else.

If you do change `--data-dir` to `/var/lib/etcd-from-backup` in the ETCD YAML file, make sure that the `volumeMounts` for `etcd-data` is updated as well, with the `mountPath` pointing to `/var/lib/etcd-from-backup` (THIS COMPLETE STEP IS OPTIONAL AND NEED NOT BE DONE FOR COMPLETING THE RESTORE).

Checks:

```bash
$ kubectl get pods,deploy,services -n default -o wide
$ kubectl get pods -n kube-system -o wide
```
