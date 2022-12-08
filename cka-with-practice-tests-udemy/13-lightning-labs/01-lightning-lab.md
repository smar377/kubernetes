### 1. Upgrade the current version of kubernetes from `1.23.0` to `1.24.0` exactly using the `kubeadm` utility. Make sure that the upgrade is carried out one node at a time starting with the `controlplane` node. To minimize downtime, the Deployment `gold-nginx` should be rescheduled on an alternate node before upgrading each node

*Hint:* Upgrade `controlplane` node first and drain node `node01` before upgrading it. Pods for `gold-nginx` should run on the `controlplane` node subsequently.

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

**STEP 3:** Drain worker node `node01`

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

**STEP 5:** 


```bash

```


### 2. 

*Hint:*

*Answer:*

```bash

```

### 3. 

*Hint:*

*Answer:*

```bash

```

### 4. 

*Hint:*

*Answer:*

```bash

```

### 5. 

*Hint:*

*Answer:*

```bash

```

### 6. 

*Hint:*

*Answer:*

```bash

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

### 10. 

*Hint:*

*Answer:*

```bash

```
