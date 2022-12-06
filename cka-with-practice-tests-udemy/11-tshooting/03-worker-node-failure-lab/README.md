### 1. Fix the broken cluster

*Hint:* Fix `node01`.

*Answer:* We check first the status of the nodes in the cluster and we see that worker node `node01` is in `NotReady` state:

```bash
$ kubectl get nodes -o wide
`````

We check then the service logs using:

```bash
journalctl -u kubelet
```

We see that the service is stopped, so we need to start it again:

```bash
$ ssh node01 "service kubelet start"
```

Finally, we check again the status of the nodes and we see that `node01` is now in `Ready` state:

```bash
$ kubectl get nodes -o wide
```

### 2. 

*Hint:*

*Answer:*

```bash

`````

### 3. 

*Hint:*

*Answer:*

```bash

`````

#
*Answer:*

```bash

`````


