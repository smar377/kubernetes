### 1. Let us explore the environment first. How many nodes do you see in the cluster?

*Answer:* Including the controlplane and worker nodes, in total there are **2** nodes.

```bash
$ kubectl get nodes -o wide
```

### 2. How many applications do you see hosted on the cluster?

*Answer:* After checking the number of deployments in the default namespace the total number of applications running is **1**.

```bash
$ kubectl get deploy -n default -o wide
```

### 3. Which nodes are the applications hosted on?

*Answer:* On both `controlplane` and `node01` nodes.

```bash
$ kubectl get pods -n default -o wide
```


### 4. We need to take `node01` out for maintenance. Empty the node of all applications and mark it unschedulable

```bash
$ kubectl drain node01 --ignore-daemonsets
```


### 5. What nodes are the apps on now?

*Answer:* Only on `controlplane` node.

```bash
$ kubectl get pods -n default -o wide
```

### 6. The maintenance tasks have been completed. Configure the node `node01` to be schedulable again

```bash
$ kubectl uncordon node01
```

### 7. How many Pods are scheduled on `node01` now?

*Answer:* There are **0** Pods scheduled on `node01` right now.

```bash
$ kubectl get pods -n default -o wide
```

### 8. Why are there no Pods on `node01`?

*Answer:* Running the `uncordon` command on a node will **NOT** automatically schedule Pods on the node. When new Pods are created, they will be placed on `node01`.

### 9. Why are the Pods placed on the `controlplane` node (check the `controlplane` node details)

*Answer:* Since there are **NO** Taints on the `controlplane` node, all the Pods were started on it when we ran the `kubectl drain node01` command. To check this, we have issued the following command:

```bash
$ kubectl describe nodes controlplane | grep -i taint
```

### 10. We need to carry out a maintenance activity on `node01` again 

Try draining the node again using the same command as before: 

```bash
$ kubectl drain node01 --ignore-daemonsets
$ kubectl get pods -o wide
```

It will **NOT** work as you see because there is a single Pod scheduled on `node01` which is not part of a `replicaset`.
The drain command will not work in this case. To forcefully drain the node we now have to use the `--force` flag.

### 11. What is the name of the Pod hosted on `node01` that is not part of a `replicaset`?

*Answer:* The `hr-app` Pod.

```bash
$ kubectl get rs -o wide
$ kubectl get pods -o wide
```

### 12. What would happen to `hr-app` if `node01` is drained forcefully?

Let's try it and for ourselves and see what happens.

*Answer:* A forceful drain of the node will delete **ANY** Pod that is not part of a ReplicaSet.

```bash
$ kubectl drain node01 --force --ignore-daemonsets
$ kubectl get pods -o wide 
```

### 13. App `hr-app is a critical one and we do not want it to be removed 

We do **NOT** want to schedule any more Pods on `node01`. Mark `node01` as unschedulable so that no new Pods are scheduled on this node. Make sure that `hr-app` is not affected.

*Answer:* Do **NOT** drain `node01, instead use the command:

```bash
$ kubectl cordon node01
```

This will ensure that no new Pods are scheduled on this node and the existing Pods will **NOT** be affected by this operation.

### 14.

```bash

```


### 15.

```bash

```
