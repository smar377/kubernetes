### 1. How many static pods exist in this cluster in all namespaces?

Run the command:

```bash
$ kubectl get pods --all-namespaces
```

and look for those with `-controlplane` appended in their name.

### 2. What is the path of the directory holding the static Pod definition files?

First idenity the kubelet config file:

```bash
$ ps -aux | grep /usr/bin/kubelet
```

From the output we can see that the kubelet config file used is `/var/lib/kubelet/config.yaml`. Next, lookup the value assigned for `staticPodPath`:

```bash
$ grep -i staticpod /var/lib/kubelet/config.yaml
```

As you can see, the path configured is the `/etc/kubernetes/manifests` directory.

### 3. Create a static Pod named `static-busybox` that uses the `busybox` image and the command `sleep 1000`

```bash
$ kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yam
```

### 4. We just created a new static Pod named `static-greenbox`. Find it and delete it. This question is a bit tricky, but if you use the knowledge you gained in the previous questions in this lab, you should be able to find the answer to it.

First, let's identify the node in which the Pod called `static-greenbox` is created. To do this, run:

```bash
$ kubectl get pods static-greenbox-node01 -o wide
```

From the result of this command, we can see that the pod is running on `node01`.
Next, SSH to `node01` and identify the path configured for static pods in this node.

**Important:** The path does NOT need to be `/etc/kubernetes/manifests`. Make sure to check the path configured in the kubelet configuration file.

```bash
# SSH to node01
$ ssh node01

# Check the path configured in the kubelet configuration file
$ ps -ef | grep /var/bin/kubelet/config.yaml

$ grep -i staticpod /var/lib/kubelet/config.yaml
staticPodPath: /etc/just-to-mess-with-you
```

Here the staticPodPath is `/etc/just-to-mess-with-you`.

Navigate to this directory and delete the YAML file:

```bash
$ cd /etc/just-to-mess-with-you/
$ las -lah
$ rm -rf greenbox.yaml
```

