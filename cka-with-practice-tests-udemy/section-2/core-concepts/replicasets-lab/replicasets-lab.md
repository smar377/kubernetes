### 1. Check the number of Pods (and the image used to deployed) existing on the system

```bash
$ kubectl get pods -o wide
```

### 2. Check the details of a Pod deployment

```bash
$ kubectl describe pod <POD_NAME>
```

### 3. Delete a Pod

```bash
$ kubectl delete pod <POD_NAME>
```

### 4. Check the number of ReplicaSets that exist on the system

```bash
$ kubectl get replicaset -o wide
```

### 5. Deploy a ReplicaSet from a YAML file

```bash
# 1st Way
$ kubectl create -f <REPLICA_SET_DEFINITION.yml>

# 2nd Way
$ kubectl apply -f <REPLICA_SET_DEFINITION.yml>
```

### 6. Delete a ReplicaSet

```bash
$ kubectl delete replicaset <REPLICA_SET_NAME>
```

### 7. Modify the contents of a ReplicaSet definition file and re-run

```bash
# 1st Way -- Edit the ReplicaSet definition as per instruction, save and then delete previous Pods so new ones can created with new characteristics
$ kubectl edit replicaset <REPLICA_SET_NAME>

# For ALL Pods in ReplicaSet
$ kubectl delete pod <POD_NAME>

##################################################

# 2nd Way -- Edit the ReplicaSet definition, copy and create new YAML file for it.
$ kubectl edit replicaset <REPLICA_SET_NAME>
$ vi <NEW_REPLICA_SET_DEFINITION.yml>

# Delete the existing ReplicaSet and re-create it, sourcing from the YAML file
$ kubectl delete replicaset <REPLICA_SET_NAME>
$ kubectl create -f <NEW_REPLICA_SET_DEFINITION.yml>
