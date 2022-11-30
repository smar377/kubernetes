### 1. How many Service Accounts exist in the default namespace?

*Answer:* There are **2** Service Accounts in the default namespace.

```bash
$ kubectl get serviceaccounts -n default
```

### 2. What is the secret token used by the default service account?

*Answer:* Run the following command and look at the `Tokens` field:

```bash
$ kubectl describe serviceaccounts default
```

### 3. We just deployed the Dashboard application. Inspect the Deployment. What is the image used by the Deployment?

*Answer:* Run the following comkmand and check the `IMAGES` column to identifdy the name of the image used:

```bash
$ kubectl get deploy -o wide
```

### 4. What is the state of the dashboard? Have the Pod details loaded successfully?

*Answer:* `Failed` with message: *pods is forbidden: User "system:serviceaccount:default:default" cannot list resource "pods" in API group "" in the namespace "default"*.

### 5. What type of account does the Dashboard application use to query the Kubernetes API?

*Answer:* As evident from the error in the web-dashboard UI, the Pod makes use of a service account to query the Kubernetes API.

### 6. Which account does the Dashboard application use to query the Kubernetes API?

*Answer:* Again, the name of the Service Account is displayed in the error message on the dashboard. The Deployment makes use of the default service account which we inspected earlier.

### 7. Inspect the Dashboard Application Pod and identify the Service Account mounted on it

*Answer:* We have to run the following command and inspect the `serviceAccount` used. The answer is **default** Service Account.

```bash
$ kubectl get pod web-dashboard-68f98dc77c-chqq5 -o yaml | grep serviceAccount
```

### 8. At what location is the ServiceAccount credentials available within the Pod?

*Hint:* Run the command `kubectl describe pod` and look for volume `mountPath`.

*Answer:* `/var/run/secrets/kubernetes.io/serviceaccount`

```bash
$ kubectl get pod web-dashboard-68f98dc77c-chqq5 -o yaml | grep mountPath
```

### 9. The application needs a ServiceAccount with the right permissions to be created to authenticate to Kubernetes. The default ServiceAccount has limited access. Create a new ServiceAccount named `dashboard-sa`

```bash
$ kubectl create serviceaccount dashboard-sa
```

### 10. 

*Hint:*

*Answer:*

```bash

```
