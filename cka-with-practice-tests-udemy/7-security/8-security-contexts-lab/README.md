### 1. What is the user used to execute the sleep process within the `ubuntu-sleeper` Pod (in the default namespace)?

*Answer:* The user used to execute the sleep process within the `ubuntu-sleeper` Pod is `root`.

```bash
$ kubectl exec ubuntu-sleeper -- whoami
$ kubectl exec -it ubuntu-sleeper -- ps -aux
```

### 2. Edit the Pod `ubuntu-sleeper` to run the sleep process with user ID `1010`

*Note:* Only make the necessary changes. Do not modify the name or image of the Pod.

*Answer:* We need to edit the Pod named `ubuntu-sleeper` and add:

```bash
secretContext:
      runAsUser: 1010
```

under the `spec -> containers` section. Then use below command to populates the change:

```bash
$ kubectl replace --force -f /tmp/kubectl-edit-3133368033.yaml
```

Verification:

```bash
$ kubectl exec -it ubuntu-sleeper -- ps -aux | grep sleep
```

### 3. A Pod definition file named `multi-pod.yaml` is given. With what user are the processes in the `web` container started?

*Note:* The Pod is created with multiple containers and security contexts defined at the Pod and Container level.

*Answer:* After looking into the YAML definition file give `multi-pod.yaml` and based on the fact that the user ID defined in the `securityContext` of the container overrides the user ID in the Pod, the correct answer is `1002`.

```bash
$ cat multi-pod.yaml
```

### 4. With what user are the processes in the `sidecar` container started?

*Note:* The Pod is created with multiple containers and security contexts defined at the Pod and Container level.

*Answer:* The correct answer is with user ID `1001`. In case of `sidecar` container, there is **NOT** any `securityContext` parameters set at container level, so the Pod one with persist which happens to exist and is set with `runAsUser: 1001`.

```bash
$ cat multi-pod.yaml
```

### 5. Update Pod `ubuntu-sleeper` to run as Root user and with the `SYS_TIME` capability

*Note:* Only make the necessary changes. Do not modify the name of the Pod.

*Answer:* Once more we need to edit the `ubuntu-sleeper-pod.yaml` and add:

```bash
capabilities:
        add: ["SYS_TIME"]
```

under the `securityContext` section. After that we need to recreate the Pod by issuing:

```bash
$ kubectl replace --force -f ubuntu-sleeper-pod.yaml
```

### 6. Now update the Pod to also make use of the `NET_ADMIN` capability

*Note:* Only make the necessary changes. Do not modify the name of the Pod.

*Answer:* Once more we need to edit the `ubuntu-sleeper-pod.yaml` and add:

```bash
capabilities:
        add: ["SYS_TIME", "NET_ADMIN"]
```

under the `securityContext` section. After that we need to recreate the Pod by issuing:

```bash
$ kubectl replace --force -f ubuntu-sleeper-pod.yaml
```

Verification:

```bash
# Get a shell into the running container
$ kubectl exec -it ubuntu-sleeper -- sh
$ ps -aux
$ cd /proc/1
$ cat status
```

The output shows capabilities bitmap for the process:

```bash
...
CapPrm:	00000000aa0435fb
CapEff:	00000000aa0435fb
...
```

Compare the capabilities of the two containers:

```bash
00000000a80425fb
00000000aa0435fb
```

In the capability bitmap of the first container, bits 12 and 25 are clear. In the second container, bits 12 and 25 are set. Bit 12 is `CAP_NET_ADMIN`, and bit 25 is `CAP_SYS_TIME`. See `capability.h` for definitions of the capability constants.
