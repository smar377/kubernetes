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

*Answer:*

```bash

```

### 7. 

*Hint:*

*Answer:*

```bash

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
