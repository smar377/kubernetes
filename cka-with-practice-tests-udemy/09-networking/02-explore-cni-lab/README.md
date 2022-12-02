### 1. Inspect the `kubelet` service and identify the container runtime value set for Kubernetes

*Answer:* The container runtime value set for Kubernetes in the `kubelet` service parameters is `remote`.

```bash
$ ps -aux | grep kubelet | grep --color container-runtime
```

### 2. What is the path configured with all binaries of CNI supported plugins?

*Answer:* The CNI binaries are located under **`/opt/cni/bin`** *by default*.

### 3. Identify which of the below plugins is **NOT** available in the list of available CNI plugins on this host

*Answer:* The correct answer to this is the `cisco` plugin.

```bash
$ ls -lah /opt/cni/bin
```

### 4. What is the CNI plugin configured to be used on this Kubernetes cluster?

*Hint:*

*Answer:* The name of the CNI plugin configured to be used on this Kubernetes cluster is `flannel`.

```bash
$ ls -lah /etc/cni/net.d/
```

### 5. What binary executable file will be run by `kubelet` after a container and its associated `namespace` are created

*Answer:* After looking at the `type` field in file `/etc/cni/net.d/10-flannel.conflist` the correct answer is `flannel`.

```bash
$ cat /etc/cni/net.d/10-flannel.conflist
```

```json
{
  "name": "cbr0",
  "cniVersion": "0.3.1",
  "plugins": [
    {
      "type": "flannel",
      "delegate": {
        "hairpinMode": true,
        "isDefaultGateway": true
      }
    },
    {
      "type": "portmap",
      "capabilities": {
        "portMappings": true
      }
    }
  ]
}
```
