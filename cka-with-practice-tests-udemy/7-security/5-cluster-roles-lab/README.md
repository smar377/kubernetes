**Note:** For the first few questions of this lab, you would have to inspect the existing ClusterRoles and ClusterRoleBindings that have been created in this cluster.

### 1. How many ClusterRoles do you see defined in the cluster?

*Answer:* There are in total **69** ClusterRoles on the cluster.

```bash
$ kubectl get clusterrole -A --no-headers | wc -l
$ kubectl get clusterrole -A --no-headers -o json | jq '.items | length'
```

### 2. How many ClusterRoleBindings exist on the cluster?

*Answer:* There are in total **54** ClusterRoleBindings on the cluster.

```bash
$ kubectl get clusterrolebindings -A --no-headers | wc -l
$ kubectl get clusterrolebindings -A --no-headers -o json | jq '.items | length'
```

### 3. What namespace is the `cluster-admin` ClusterRole part of?

*Answer:* Cluster Roles are cluster wide and **NOT** part of any namespace.

### 4. What user/groups are the `cluster-admin` ClusterRole bound to?

*Hint:* The ClusterRoleBinding for the Role is with the same name.

*Answer:* The `cluster-admin` ClusterRole is bound to `system:masters` group.

```bash
$ kubectl describe clusterrolebindings cluster-admin
```

### 5. What level of permission does the `cluster-admin` Role grant?

*Hint:* Inspect the `cluster-admin` Role's privileges.

*Answer:* Perform any action on any resource in the cluster.

```bash
$ kubectl describe clusterrole cluster-admin
```

### 6. A new user `michelle` joins the team. She will be focusing on the `nodes` in the cluster. Create the required `ClusterRoles` and `ClusterRoleBindings` so she gets access to the nodes

*Answer:* We will use the command `kubectl create` to create a ClusterRole and ClusterRoleBinding for user `michelle` to grant access to the nodes.
After that, we will test the access using the command `kubectl auth can-i list nodes --as michelle`:

```bash
$ kubectl create -f node-admin-clusterrole.yaml
$ kubectl create -f michelle-clusterrolebinding.yaml
```

Verification and testing:

```bash
$ kubectl get clusterrole node-admin
$ kubectl get clusterrolebindings michelle-binding
$ kubectl describe clusterrole node-admin
$ kubectl describe clusterrolebindings michelle-binding
$ kubectl auth can-i list nodes --as michelle
```

### 7. User `michelle's` responsibilities are growing and now she will be responsible for storage as well. Create the required ClusterRoles and ClusterRoleBindings to allow her access to Storage

*Hint:* Get the API groups and resource names from command `kubectl api-resources`.

*Answer:* We will use the command `kubectl create` to create a new ClusterRole and ClusterRoleBinding. Then we will assign the correct resources and verbs. After that, we will test the access using the command `kubectl auth can-i list storageclasses --as michelle`.

```bash
$
$
$
$
$
```

Verification and testing:

```bash
$
$
$
$
$
```

### 8. 

*Hint:*

*Answer:*

```bash

```

### 9. 

*Hint:*

*Answer:*

```bash

```

### 10. 

*Hint:*

*Answer:*

```bash

```
