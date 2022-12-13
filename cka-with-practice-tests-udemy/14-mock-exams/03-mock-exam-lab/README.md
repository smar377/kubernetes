### 1. Create a new Service Account with the name `pvviewer`. Grant this Service Account access to `list` all PersistentVolumes in the cluster by creating an appropriate `ClusterRole` called `pvviewer-role` and `ClusterRoleBinding` called `pvviewer-role-binding`. Next, create a Pod called `pvviewer` with image `redis` and Service Account `pvviewer` in the default namespace

*Answer:* Let's first create the new Service Account named `pvviewer`:

```bash
$ kubectl create serviceaccount pvviewer
$ kubectl get sa -o wide
```

Let's now create an appropriate ClusterRole and ClusterRoleBinding in order to grant this ServiceAccount access to list all PersistentVolumes in the cluster:

```bash
# Create and verify ClusterRole
$ kubectl create clusterrole pvviewer-role --verb=list --resource=persistentvolumes
$ kubectl get clusterrole pvviewer-role
$ kubectl describe clusterrole pvviewer-role

# Create and verify ClusterRoleBinding
$ kubectl create clusterrolebinding pvviewer-role-binding --clusterrole=pvviewer-role --user=pvviewer
$ kubectl get clusterrolebindings pvviewer-role-binding
$ kubectl describe clusterrolebindings pvviewer-role-binding
```

Create a Pod named `pvviewer` with image `redis` and serviceAccount `pvviewer` in the default namespace:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvviewer
spec:
  containers:
  - image: redis
    name: pvviewer
  serviceAccountName: pvviewer
```

```bash
# Create the Pod
$ kubectl create -f pvviewer-pod-sa.yaml

# Verify
$ kubectl get pod pvviewer
$ kubectl describe pod pvviewer
$ kubectl auth can-i list persistentvolumes --as=pvviewer -n default
```

### 2. List the `InternalIP` of all nodes of the cluster. Save the result to a file `/root/CKA/node_ips`

*Hint:* Answer should be in the following format: 

- `InternalIP` of `controlplane` *__space__* `InternalIP` of `node01` (in a single line)

*Answer:*

```bash
$ kubectl get nodes -o wide -o=jsonpath='{.items[*].status.addresses[0].address}' > /root/CKA/node_ips
```

### 3. Create a Pod called `multi-pod` with two containers

- Container 1:
  - `name: alpha`
  - `image: nginx`
- Container 2:
  - `name: beta`
  - `image: busybox`
  - `command: sleep 4800`

*Environment Variables:*

- Container 1:
  - `name: alpha`

- Container 2:
  - `name: beta`

*Answer:* We will build first a YAML definition file for this multi-container Pod as per follows:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  containers:
  - name: alpha
    image: nginx
    env:
    - name: name
      value: alpha
  - name: beta
    image: busybox
    command: ['sleep', '4800']
    env:
    - name: name
      value: beta
```

and then we will create that Pod by issuing:

```bash
$ kubectl create -f multi-pod.yaml
```

Verification:

```bash
$ kubectl get pod multi-pod -o wide
$ kubectl describe pod multi-pod
```

### 4. Create a Pod called `non-root-pod` with `redis:alpine` image

Specifications:

- `runAsUser: 1000`
- `fsGroup: 2000`

*Answer:* We build first a YAML definition file for the Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: non-root-pod 
spec:
  securityContext:
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - name: non-root-pod
    image: redis:alpine
```

and then we create the Pod by issuing:

```bash
$ kubectl create -f non-root-pod.yaml
```

Verification:

```bash
$ kubectl get pod non-root-pod
$ kubectl describe pod non-root-pod
```

### 5. We have deployed a new Pod called `np-test-1` and a Service called `np-test-service`. Incoming connections to this service are **NOT** working. Troubleshoot and fix it

***Important:*** Don't delete any current objects deployed.

*Hint:* Create NetworkPolicy, by the name `ingress-to-nptest` that allows incoming connections to the Service over port 80.

*Answer:*

```bash

```

### 6. Taint the worker node `node01` to be `Unschedulable`. Once done, create a Pod called `dev-redis` with image `redis:alpine` to ensure workloads are **NOT** scheduled to this worker node. Finally, create a new Pod called `prod-redis` with image `redis:alpine` with Toleration to be scheduled on `node01`.

- `key: env_type`
- `value: production`
- `operator: Equal`
- `effect: NoSchedule`

*Hint:*

*Answer:*

```bash

```

### 7. Create a Pod called `hr-pod` in `hr` namespace belonging to the `production` environment and `frontend` tier

- `image: redis:alpine`

*Hint:* Use appropriate labels and create all the required objects if they do not exist in the system already.

*Answer:*

```bash

```

### 8. A `kubeconfig` file called `super.kubeconfig` has been created under `/root/CKA`. There is something wrong with the configuration. Troubleshoot and fix it

*Hint:*

*Answer:*

```bash

```

### 9. We have created a new Deployment called `nginx-deploy`. Scale the Deployment to **3** replicas. Has the replica number increased? Troubleshoot the issue and fix it

*Hint:*

*Answer:*

```bash

```
