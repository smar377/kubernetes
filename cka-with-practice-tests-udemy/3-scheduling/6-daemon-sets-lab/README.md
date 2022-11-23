### 1. How many DaemonSets are created in the cluster in all namespaces? (Check all namespaces)

```bash
# 1st Way
$ kubectl get daemonsets --all-namespaces

# 2nd Way - Short version
$ kubectl get ds --all-namespaces
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
