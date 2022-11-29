### 1. Inspect the environment and identify the authorization modes configured on the cluster

*Hint:* Check the `kube-apiserver` settings.

*Answer:* The authorization modes configured are:
- Node
- RBAC

```bash
$ kubectl describe pod kube-apiserver-controlplane -n kube-system | grep '\-\-authorization-mode' 
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep '\-\-authorization-mode'
$ ps -aux | grep authorization
```

### 2. How many Roles exist in the default namespace?

*Answer:* There are **NO** roles configures in the default namespace.

```bash
$ kubectl get role -n default -o wide
```

### 3. How many Roles exist in all namespaces together?

*Answer:* There are **12** roles configured in ALL namespaces.

```bash
$ kubectl get role -A --no-headers | wc -l
$ kubectl get roles --all-namespaces --no-headers | wc -l
```

### 4. What are the resources the `kube-proxy` Role in the `kube-system` namespace is given access to?

*Answer:* Role `kube-proxy` has access to `configmaps` in the `kube-system` namespace.

```bash
$ kubectl describe role kube-proxy -n kube-system
```

### 5. What actions can the `kube-proxy` role perform on `configmaps`?

*Answer:* The only action `kube-proxy` role can perform on `configmaps` is `get`.

```bash
$ kubectl describe role kube-proxy -n kube-system
```

### 6. Which account is the `kube-proxy` Role assigned to?

*Answer:* Role `kube-proxy` is assigned to `Group:system:bootstrappers:kubeadm:default-node-token` account.

```bash
$ kubectl describe rolebinding kube-proxy -n kube-system
```

### 7. A user named `dev-user` is created. User's details have been added to the `kubeconfig` file

Inspect the permissions granted to the user. Check if the user can list Pods in the default namespace.

*Hint:* Use the `--as dev-user` option with `kubectl` to run commands as the `dev-user`.

*Answer:* User named `dev-user` **CANNOT** list Pods in the default namespace.

```bash
$ kubectl get pod --as dev-user -n default
$ kubectl auth can-i get pods -n default --as dev-user
```

### 8. Create the necessary Roles and RoleBindings required for the `dev-user` to create, list and delete Pods in the `default` namespace

*Answer:* We will present two ways of solving this task.

- **1st WAY** - Create both Role and RoleBinding from command line via `kubectl`

```bash
# Create a Role
$ kubectl create role developer --namespace=default --verb=list,create,delete --resource=pods

# Create a RoleBinding
$ kubectl create rolebinding dev-user-binding --namespace=default --role=developer --user=dev-user
```

- **2nd WAY** - Create both `Role` and `RoleBinding` by using YAML manifest files

```bash
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "create","delete"]
```

```bash
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

Last we need to create them by issuing:

```bash
$ kubectl create -f developer-role.yaml
$ kubectl create -f dev-user-binding.yaml
```

Verification:

```bash
$ kubectl get roles,rolebinding -n default
$ kubectl describe role developer
$ kubectl describe rolebindings dev-user-binding 
```

Testing:

```bash
$ kubectl auth can-i list pods -n default --as dev-user
$ kubectl auth can-i create pods -n default --as dev-user
$ kubectl auth can-i delete pods -n default --as dev-user
```

### 9. A set of new Roles and RoleBindings are created in the `blue` namespace for the `dev-user`. However, the `dev-user` is unable to get details of the `dark-blue-app` Pod in the `blue` namespace. Investigate and fix the issue

*Hint:* New `Roles` and `RoleBindings` are created in the blue namespace. Check out the `resourceNames` configured on the role.

*Answer:* Run below command and correct the `resourceNames` field. Meaning we need to add also as additional `resourceName` the `dark-blue-app` Pod:

```bash
$ kubectl edit role developer -n blue
```

### 10. Add a new rule in the existing Role `developer` to grant the `dev-user` permissions to create Deployments in the `blue` namespace

*Hint:* Remember to add api group "apps".

*Answer:* Edit once more the `developer` Role and append new rule at the end of the file: 

```bash
$ kubectl edit role developer -n blue
```

The file will look like this:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: "2022-11-29T13:14:13Z"
  name: developer
  namespace: blue
  resourceVersion: "4560"
  uid: b51ef655-da0f-441c-9fde-47c9805ab9d6
rules:
- apiGroups:
  - ""
  resourceNames:
  - blue-app
  - dark-blue-app
  resources:
  - pods
  verbs:
  - get
  - watch
  - create
  - delete
- apiGroups:
  - apps
  resources:
  - deployments
  verbs:
  - get
  - watch
  - create
  - delete
```

Verification and testing:

```bash
$ kubectl describe role developer -n blue
$ kubectl auth can-i get deploy --as dev-user -n blue
$ kubectl auth can-i watch deploy --as dev-user -n blue
$ kubectl auth can-i create deploy --as dev-user -n blue
$ kubectl auth can-i delete deploy --as dev-user -n blue
```
