### 1. How many Secrets exist on the system? In the current (default) namespace

```bash
$ kubectl get secrets -n default -o wide
```

### 2. How many secrets are defined in the `dashboard-token` secret?

*Answer:* There are three secrets - `ca.crt`, `namespace`, and `token`

```bash
$ kubectl describe secrets dashboard-token
```

### 3. What is the type of the `dashboard-token` secret?

*Answer:* `kubernetes.io/service-account-token`

```bash
$ kubectl describe secrets dashboard-token | grep -i type
```

### 4. Which of the following is not a secret data defined in `dashboard-token` secret?

*Answer:* `Type` 

### 5. We are going to deploy an application with the below architecture

We have already deployed the required Pods and Services. Check out the Pods and Services created. Check out the web application using the Webapp MySQL link above your terminal, next to the Quiz Portal Link.

The reason the application is failed is because we have not created the secrets yet. Create a new `secret` named `db-secret` with the data given below.
You may follow any one of the methods discussed in lecture to create the secret.

```bash
# 1st Way - Create secret directly from the command line
$ kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123

# Check the newly created secrets 
$ kubectl get secrets db-secret
$ kubectl describe secrets db-secret
```

```bash
# 2nd Way - Using dry-run and exporting in a YAML definition file
$ kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123 --dry-run=client -o yaml > db-secret-definition.yaml

# Create the new secret and check
$ kubectl create -f db-secret-definition.yaml
$ kubectl get secrets db-secret
$ kubectl describe secrets db-secret
```

### 6. Configure `webapp-pod` to load environment variables from the newly created secret

Delete and recreate the pod if required.

```bash
# 1st Way - Edit the Pod and use replace command to force recreation of the Pod (edit is forbidden but we can use the /tmp/file to create the new Pod

$ kubectl edit pod webapp-pod 
error: pods "webapp-pod" is invalid
A copy of your changes has been stored to "/tmp/kubectl-edit-56600331.yaml"
error: Edit cancelled, no valid changes were saved.

$ kubectl replace --force -f /tmp/kubectl-edit-56600331.yaml 
pod "webapp-pod" deleted
pod/webapp-pod replaced

# Check if creation was successful
$ kubectl get secrets -n default -o wide
$ kubectl get secrets db-secret -o yaml
```

```bash
# 2nd Way - Create a YAML Pod definition file without the secret reference using the --dry-run=client flag
$ kubectl run webapp-pod --image=kodekloud/simple-webapp-mysql --labels='name=webapp-pod' --dry-run=client -o yaml > webapp-pod-env-from-secret.yaml

# Edit the YAML file and add the secret reference field
[output ommited]
envFrom:
      - secretRef:
          name: db-secret
[output ommited]

# Delete Pod and recreate it
$ kubectl delete pod webapp-pod
$ kubectl create -f webapp-pod-env-from-secret.yaml

# Check if creation was successful
$ kubectl get secrets -n default -o wide
$ kubectl get secrets db-secret -o yaml
```
