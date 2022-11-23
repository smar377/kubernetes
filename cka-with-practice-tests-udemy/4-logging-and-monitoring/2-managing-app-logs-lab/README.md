### 1. Identify the user access issues (`USER5` in the example)

Let us inspect the logs of the Pod:

```bash
$ kubectl logs -f webapp-1
```

After checking the logs we see multiple events related to `USER5` indicating that:

`USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.`

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
# 1st Way
$ kubectl get serviceaccount my-scheduler -n kube-system

# 2nd Way - Short version
$ kubectl get sa -n my-scheduler kube-system

$ kubectl get clusterrolebinding
```

*Note:* Don't worry if you are not familiar with these resources. We will cover it later on.

### 4. Let's create a `ConfigMap` that the new scheduler will employ using the concept of `ConfigMap` as a volume

We have already given a configMap definition file called `my-scheduler-configmap.yaml` at `/root/` path that will create a configmap with name `my-scheduler-config` using the content of file `/root/my-scheduler-config.yaml`.

```bash
# 1st Way - Create the ConfigMap object
$ kubectl create -f /root/my-scheduler-configmap.yaml

# 2nd Way - Create the ConfigMap object
$ kubectl create configmap my-scheduler-config --from-file=/root/my-scheduler-config.yaml -n kube-system

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

### 6. A Pod definition file is given. Use it to create a Pod with the new custom scheduler (file located at `/root/nginx-pod.yaml`)

Edit the `nginx-pod.yaml` file like this:

```yaml
---
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  schedulerName: my-scheduler
  containers:
  - image: nginx
    name: nginx
```

and create the Pod by issuing the following command:

```bash
$ kubectl create -f nginx-pod.yaml
```

Then, we can check the status of the Pod by running:

```bash
$ kubectl get pods -A | grep nginx
```

**References:**

- https://github.com/kubernetes/community/blob/master/contributors/devel/sig-scheduling/scheduling_code_hierarchy_overview.md
- https://kubernetes.io/blog/2017/03/advanced-scheduling-in-kubernetes/
- https://jvns.ca/blog/2017/07/27/how-does-the-kubernetes-scheduler-work/
- https://stackoverflow.com/questions/28857993/how-does-kubernetes-scheduler-work
