### 1. A Pod called `rabbit` is deployed. Identify the CPU requirements set on the Pod in the current (default) namespace

```bash
$ kubectl describe pod rabbit
```

### 2. Delete the `rabbit` Pod. Once deleted, wait for the pod to fully terminate

```bash
# Delete the Pod
$ kubectl delete pod rabbit

# Check running Pods and make sure it's terminated
$ kubectl get pods --watch
```

### 3. Another Pod called `elephant` has been deployed in the default namespace. It fails to get to a running state. Inspect this Pod and identify the reason why it is not running

```bash
$ kubectl describe pod elephant  | grep -A5 State:
```

It's changing the status frequently so make use of the `watch` command to get the output every two seconds:

```bash
$ watch kubectl get pods
```

*Note:* - Make use of the `CTRL + C` key to exit from the process.

### 4. The status `OOMKilled` indicates that it is failing because the Pod ran out of memory. Identify the memory limit set on the Pod

*Answer:* `Memory: 10Mi`

```bash
$ kubectl describe pod elephant
```

### 5. The `elephant` Pod runs a process that consume `15Mi` of memory. Increase the limit of the `elephant` Pod to `20Mi`. Delete and recreate the Pod if required. Do not modify anything other than the required fields.

```bash
# Create the file elephant-pod.yaml by the following command and edit the file such as memory limit is set to 20Mi as follows
$ kubectl get pods elephant -o yaml > elephant-pod.yaml
```

```bash
# Then delete the existing one first and recreate a new one from the YAML file
$ kubectl replace -f elephant-pod.yaml --force
```
