### 1. Fix the broken cluster

*Answer:* We check first the status of the nodes in the cluster and we see that worker node `node01` is in `NotReady` state:

```bash
$ kubectl get nodes -o wide
`````

We check then the service logs of `node01` by using:

```bash
$ ssh node01
$ journalctl -u kubelet
```

We see that the service is stopped, so we need to start it again:

```bash
$ ssh node01 "service kubelet start"
```

Finally, we check again the status of the nodes and we see that `node01` is now in `Ready` state:

```bash
$ kubectl get nodes -o wide
```

### 2. The cluster is broken again. Investigate and fix the issue

*Answer:* We start again by checking the status of the nodes in the cluster and we see that worker node `node01` is in `NotReady` state:

```bash
$ kubectl get nodes -o wide
`````

Let's now inspect its logs to see if we can get more information on what might be the issue:

```bash
$ kubectl describe nodes node01
```

It seems that `kubelet` has stopped running on `node01` again. Since this is a `systemd` managed system, we can check the `kubelet` logs by running `journalctl`. Here is a snippet showing the error with `kubelet`:

```bash
$ journalctl -u kubelet -f

Dec 06 11:03:39 node01 kubelet[6136]: Error: failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory
Dec 06 11:03:50 node01 kubelet[6168]: Error: failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory
```

There appears to be a mistakend path used for the CA certificate in the `kubelet` configuration. This can be corrected by updating the file `/var/lib/kubelet/config.yaml`. Once this is fixed, restart the kubelet service, and `node01` should return back to a working state:

```bash
$ ssh node01
$ vi /var/lib/kubelete/config.yaml
$ service kubelet restart && service kubelet status
$ exit
$ kubectl get nodes -o wide
```

### 3. The cluster is broken again. Investigate and fix the issue

*Answer:* We start by checking the nodes and we see that noce more worker node `node01` is in `NotReady` state:

```bash
$ kubectl get nodes -o wide
`````

Once again the `kubelet` service has stopped working. Checking the logs, we can see that this time, it is not able to reach the `kube-apiserver`:

```bash
$ ssh node01
$ journalctl -u kubelet -f

Dec 06 11:13:30 node01 kubelet[7138]: W1206 11:13:30.769935    7138 reflector.go:324] vendor/k8s.io/client-go/informers/factory.go:134: failed to list *v1.Service: Get "https://controlplane:6553/api/v1/services?limit=500&resourceVersion=0": dial tcp 10.31.103.12:6553: connect: connection refused
Dec 06 11:13:30 node01 kubelet[7138]: E1206 11:13:30.770033    7138 reflector.go:138] vendor/k8s.io/client-go/informers/factory.go:134: Failed to watch *v1.Service: failed to list *v1.Service: Get "https://controlplane:6553/api/v1/services?limit=500&resourceVersion=0": dial tcp 10.31.103.12:6553: connect: connection refused
```

As we can clearly see, `kubelet` is trying to connect to the API server on the `controlplane` node on `port 6553`. This is incorrect.

To fix it, we need to correct the port on the `kubeconfig` file used by the kubelet which is located at `/etc/kubernetes/kubelet.conf`:

```bash
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data:
    --REDACTED---
    server: https://controlplane:6443
```

Last, we need to restart the `kubelet` service for the change to take effect. Then we check again the statuses of the nodes to see if the problem is now solved:

```bash
$ service kubelet restart && service kubelet status
$ kubectl get nodes -o wide
```
#
*Answer:*

```bash

`````


