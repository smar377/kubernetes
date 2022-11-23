### 1. What is the name of the Pod that deploys the default kubernetes scheduler in this environment?

```bash
$ kubectl get pods --all-namespaces -o wide
```

### 2. What is the image used to deploy the Kubernetes scheduler? Inspect the kubernetes scheduler Pod and identify the image

```bash
$ kubectl describe pod kube-scheduler-controlplane -n kube-system | grep -i image
```

### 3. Quick check of `ServiceAccount` and `ClusterRoleBinding` objects that our custom scheduler will make use of

Checkout the following Kubernetes objects:

- ServiceAccount: my-scheduler (kube-system namespace)
- ClusterRoleBinding: my-scheduler-as-kube-scheduler
- ClusterRoleBinding: my-scheduler-as-volume-scheduler

Run the commands:

```bash
$ kubectl get serviceaccount -n kube-system
$ kubectl get clusterrolebinding
```

*Note:* Don't worry if you are not familiar with these resources. We will cover it later on.

### 4. Let's create a configmap that the new scheduler will employ using the concept of `ConfigMap` as a volume

We have already given a configMap definition file called `my-scheduler-configmap.yaml` at `/root/` path that will create a configmap with name `my-scheduler-config` using the content of file `/root/my-scheduler-config.yaml`.

```bash
# Create the ConfigMap object
$ kubectl create -f /root/my-scheduler-configmap.yaml

# Check if creation was successfull
$ kubectl get configmaps --all-namespaces | grep my-scheduler-config
```

### 5. Deploy an additional scheduler to the cluster following the given specification

Use the manifest file provided at `/root/my-scheduler.yaml`. Use the same image as used by the default kubernetes scheduler.

Edit specified file and change `image` field to `k8s.gcr.io/kube-scheduler:v1.24.0`

Then all we need to do is to deploy it by running:

```bash
$ kubectl create -f my-scheduler.yaml
```

Last, check is the deployment was successful:

```bash
$ kubectl get pods --all-namespaces | grep my-scheduler
```

### 6. A Pod definition file is given. Use it to create a POD with the new custom scheduler (file located at `/root/nginx-pod.yaml`)



```bash

```
