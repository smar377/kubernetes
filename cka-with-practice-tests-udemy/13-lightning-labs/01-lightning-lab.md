### 1. Upgrade the current version of kubernetes from `1.23.0` to `1.24.0` exactly using the `kubeadm` utility. Make sure that the upgrade is carried out one node at a time starting with the `controlplane` node. To minimize downtime, the Deployment `gold-nginx` should be rescheduled on an alternate node before upgrading each node

*Hint:* Upgrade `controlplane` node first and drain node `node01` before upgrading it. Pods for `gold-nginx` should run on the `controlplane` node subsequently.

#### Upgrading control plane node(s)

**STEP 1:** Determine which version to upgrade to (in our case we need `version 1.24.0-00`)

```bash
$ apt update
$ apt-cache madison kubeadm
```

**STEP 2:** Upgrade the `controlplane` node

***Important:*** *The upgrade procedure on control plane nodes should be executed one node at a time. Pick a control plane node that you wish to upgrade first. It must have the /etc/kubernetes/admin.conf file.*

- Upgrade `kubeadm`:

```bash
$ apt-mark unhold kubeadm && \
$ apt-get update && apt-get install -y kubeadm=1.24.0-00 && \
$ apt-mark hold kubeadm
```

- Verify that the download works and has the expected version:

```bash
$ kubeadm version
```

- Verify the upgrade plan:

```bash
$ kubeadm upgrade plan
```

This command checks that your cluster can be upgraded, and fetches the versions you can upgrade to. It also shows a table with the component config version states.

- Choose a version to upgrade to, and run the appropriate command:

```bash
$ kubeadm upgrade apply v1.24.0
```

- Manually upgrade your CNI provider plugin

Your Container Network Interface (CNI) provider may have its own upgrade instructions to follow. Check the addons page to find your CNI provider and see whether additional upgrade steps are required.

This step is not required on additional control plane nodes if the CNI provider runs as a DaemonSet.

**STEP 3:** Drain control plane node `controlplane`

- Prepare the node for maintenance by marking it unschedulable and evicting the workloads:

```bash
$ kubectl drain controlplane --ignore-daemonsets
```

**STEP 4:** Upgrade `kubelet` and `kubectl`

- Upgrade the `kubelet` and `kubectl`:

```bash
$ ssh controlplane
$ apt-mark unhold kubelet kubectl && \
$ apt-get update && apt-get install -y kubelet=1.24.0-00 kubectl=1.24.0-00 && \
$ apt-mark hold kubelet kubectl
```

- Restart the `kubelet`:

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl restart kubelet
```

**STEP 5:** Uncordon the `controlplane` node

- Bring the node back online by marking it schedulable:

```bash
$ kubectl uncordon controlplane
```

#### Upgrading worker node(s)

**STEP 1:** Upgrade kubeadm

- Upgrade kubeadm:

```bash
$ ssh node01
$ apt-mark unhold kubeadm && \
$ apt-get update && apt-get install -y kubeadm=1.24.0-00 && \
$ apt-mark hold kubeadm
```

**STEP 2:** Call "kubeadm upgrade"

- For worker nodes this upgrades the local kubelet configuration:

```bash
$ sudo kubeadm upgrade node
```

**STEP 3:** Drain the node

- Prepare the node for maintenance by marking it unschedulable and evicting the workloads:

```bash
$ kubectl drain node01 --ignore-daemonsets
```

**STEP 4:** Upgrade `kubelet` and `kubectl`

- Upgrade the `kubelet` and `kubectl`:

```bash
$ ssh node01
$ apt-mark unhold kubelet kubectl && \
$ apt-get update && apt-get install -y kubelet=1.24.0-00 kubectl=1.24.0-00 && \
$ apt-mark hold kubelet kubectl
```

- Restart the `kubelet`:

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl restart kubelet
```

**STEP 5:** Uncordon the worker node `node01`

- Bring the node back online by marking it schedulable:

```bash
$ kubectl uncordon controlplane
```

Last, let's verify the status of the cluster and the fact that Pods of `gold-nginx` Deployment is running on `controlplane` node:

```bash
$ kubectl get node -o wide
$ kubectl get pods,deploy -o wide
```

### 2. 

*Hint:*

*Answer:*

```bash

```

### 3. A `kubeconfig` file called `admin.kubeconfig` has been created in `/root/CKA`. There is something wrong with the configuration. Troubleshoot and fix it

*Hint:*

*Answer:*

```bash

```

### 4. Create a new Deployment called `nginx-deploy`, with image `nginx:1.16` and `1 replica`. Next upgrade the Deployment to `version 1.17` using rolling update

*Hint:*

*Answer:*

```bash

```

### 5. A new Deployment called `alpha-mysql` has been deployed in the `alpha` namespace. However, the Pods are not running. Troubleshoot and fix the issue. The Deployment should make use of the persistentVolume `alpha-pv` to be mounted at `/var/lib/mysql` and should use the environment variable `MYSQL_ALLOW_EMPTY_PASSWORD=1` to make use of an empty root password

***Important:*** Do not alter the persistent volume.

*Answer:*

```bash

```

### 6. Take the backup of ETCD at the location `/opt/etcd-backup.db` on the `controlplane` node

*Hint:*

*Answer:*

```bash

```

### 7. Create a Pod called `secret-1401` in the `admin1401` namespace using the `busybox` image. The container within the Pod should be called `secret-admin` and should sleep for `4800` seconds

The container should mount a `read-only` secret volume called `secret-volume` at the path `/etc/secret-volume`. The secret being mounted has already been created for you and is called `dotfile-secret`.

*Hint:*

*Answer:*

```bash

```
