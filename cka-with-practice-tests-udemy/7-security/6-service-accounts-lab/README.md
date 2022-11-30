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

### 10. Enter the access token in the UI of the dashboard application. Click Load Dashboard button to load Dashboard

Create an authorization token for the newly created service account, copy the generated token and paste it into the token field of the UI.

*Hint:* To do this, run `kubectl create token dashboard-sa` for the `dashboard-sa` service account, copy the token and paste it in the UI.

*Answer:*

```bash
$ kubectl create token dashboard-sa
```

### 11. You shouldn't have to copy and paste the token each time 

The Dashboard application is programmed to read token from the secret mount location. However currently, the default service account is mounted. Update the Deployment to use the newly created ServiceAccount.

*Hint:* Edit the deployment to change ServiceAccount from default to `dashboard-sa`.

*Asnwer:* We will present two different ways to solve this task:

**1st WAY** - Directly leveraging the command line and `kubectl` (*imperative way*)

```bash
# Edit the Deployment and specify the serviceAccountName field with value as dashbpard-sa inside the Pod spec
$ kubectl edit deploy web-dashboard

OR

# Run the following command to use the newly created service account 
$ kubectl set serviceaccount deploy/web-dashboard dashboard-sa
```

**2nd WAY** - Via manifest YAML file and `kubectl` (*declarative way*)

```bash

```
