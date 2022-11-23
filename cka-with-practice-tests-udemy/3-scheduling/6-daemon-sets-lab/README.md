### 1. How many DaemonSets are created in the cluster in all namespaces? (Check all namespaces)

```bash
$ kubectl get daemonsets --all-namespaces
```

### 2. On how many nodes are the Pods scheduled by the DaemonSet `kube-proxy`?

```bash
$ kubectl describe daemonset kube-proxy --namespace=kube-system
```

### 3. What is the image used by the Pod deployed by the `kube-flannel-ds` DaemonSet?

```bash
$ kubectl describe daemonset kube-flannel-ds --namespace=kube-system
```

### 4. Deploy a DaemonSet for FluentD Logging. Use the given specifications

An easy way to create a DaemonSet is to first generate a YAML file for a Deployment with the command: 

```bash
$ kubectl create deployment elasticsearch --image=k8s.gcr.io/fluentd-elasticsearch:1.20 -n kube-system --dry-run=client -o yaml > fluentd.yaml
```
 
Next, remove the replicas, strategy and status fields from the YAML file using a text editor. Also, change the kind from Deployment to DaemonSet.

Finally, create the Daemonset by running:

```bash
$ kubectl create -f fluentd.yaml
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
