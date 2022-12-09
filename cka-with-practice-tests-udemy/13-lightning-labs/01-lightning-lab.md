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
$ apt-mark unhold kubeadm &&
$ apt-get update && apt-get install -y kubeadm=1.24.0-00 &&
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
$ apt-mark unhold kubelet kubectl &&
$ apt-get update && apt-get install -y kubelet=1.24.0-00 kubectl=1.24.0-00 &&
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
$ apt-mark unhold kubeadm &&
$ apt-get update && apt-get install -y kubeadm=1.24.0-00 &&
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
$ apt-mark unhold kubelet kubectl &&
$ apt-get update && apt-get install -y kubelet=1.24.0-00 kubectl=1.24.0-00 &&
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
$ kubectl describe pod gold-nginx-7cf65dbf6d-g9xdx
```

Paradoxically, once we drain the `node01` in order to prepare it for maintenance, we see that the Pod hosted there of the `gold-nginx` Deployment is not being moved to `controlplane`. After doing some investigation, we noticed that `controlplane` has a Taint of `NoSchedule` configured that wasn't permitting the Pod to be created there leaving it in a `Pending` state. 

We solve this by removing that Taint and then we check again:

```bash
# Check if controlplane has any Taints
$ kubectl describe nodes controlplane | grep -i taints

# Untaint controlplane node
$ kubectl taint node controlplane node-role.kubernetes.io/control-plane:NoSchedule-

# Check again
$ kubectl get pods,deploy -o wide
```

### 2. Print the names of all Deployments in the `admin2406` namespace in the following format

| DEPLOYMENT  | CONTAINER_IMAGE | READY_REPLICAS | NAMESPACE |
| ------------- | ------------- | ------------- | ------------- |
| *deployment name*  | *container image used*  | *ready replica count* | *namespace* |

The data should be sorted by the increasing order of the Deployment name.

***Example:***

| DEPLOYMENT  | CONTAINER_IMAGE | READY_REPLICAS | NAMESPACE |
| ------------- | ------------- | ------------- | ------------- |
| deploy1  | nginx  | 1 | admin2406 |
  
Write the result to the file `/opt/admin2406_data`.

*Answer:* We should use the following command:

```bash
$ kubectl get deploy -n admin2406 --sort-by=.metadata.name -o=custom-columns=DEPLOYMENT:.metadata.name,CONTAINER_IMAGE:.spec.template.spec.containers[*].image,READY_REPLICAS:.status.readyReplicas,NAMESPACE:.metadata.namespace > /opt/admin2406_data
```

### 3. A `kubeconfig` file called `admin.kubeconfig` has been created in `/root/CKA`. There is something wrong with the configuration. Troubleshoot and fix it

*Answer:* We noticed that the port configured for the Kube API server is wrongly set to `4380`. We need to edit the given `kube-config` file and correct it to (default) `6443`:

```bash
$ kubectl config view --kubeconfig /root/CKA/admin.kubeconfig
$ vi /root/CKA/admin.kubeconfig
```

### 4. Create a new Deployment called `nginx-deploy`, with image `nginx:1.16` and `1 replica`. Next upgrade the Deployment to `version 1.17` using rolling update

*Answer:* We first create the requested Deployment via the *imperative* way as follows:

```bash
# Creation
$ kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1

# Verification  
$ kubectl get deploy -n default -o wide  
```

Next, in order to upgrade the Deployment to `version 1.17` using `RollingUpdate`:

```bash
$ kubectl describe deploy nginx-deploy | grep -i strategy
$ kubectl set image deploy nginx-deploy nginx=nginx:1.17
```

The strategy used is `RollingUpdate` (default) so all we need to do is to set the image of the Deployment to `version 1.17` and that is all.
Another way to do this is by editing the Deployment.

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
