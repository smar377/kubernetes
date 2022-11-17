### 1. Check the number of Pods (and the image used to deployed) running on the system

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

### 4. Check the number of ReplicaSets running on the system

```bash
$ kubectl get replicaset -o wide
```


### 5. Check the details of a ReplicaSet deployment

```bash
$ kubectl describe replicaset <REPLICA_SET_NAME>
```

### 6. Deploy a ReplicaSet from a YAML file

```bash
# 1st Way
$ kubectl create -f <REPLICA_SET_DEFINITION.yml>

# 2nd Way
$ kubectl apply -f <REPLICA_SET_DEFINITION.yml>
```

### 7. Delete a ReplicaSet

```bash
$ kubectl delete replicaset <REPLICA_SET_NAME>
```

### 8. Modify the contents of a ReplicaSet definition file and re-run

```bash
# 1st Way -- Edit the ReplicaSet definition as per instruction, save and then delete previous Pods so new ones can created with new characteristics
$ kubectl edit replicaset <REPLICA_SET_NAME>

# For ALL Pods in ReplicaSet
$ kubectl delete pod <POD_NAME>
```

```bash
# 2nd Way -- Edit the ReplicaSet definition, copy and create new YAML file for it.
$ kubectl edit replicaset <REPLICA_SET_NAME>
$ vi <NEW_REPLICA_SET_DEFINITION.yml>

# Delete the existing ReplicaSet and re-create it, sourcing from the YAML file
$ kubectl delete replicaset <REPLICA_SET_NAME>
$ kubectl create -f <NEW_REPLICA_SET_DEFINITION.yml>
```

### 9. Scale the ReplicaSet

```bash
# 1st Way -- Edit the ReplicaSet YAML definition file and increase/decrease the number of "replicas" key
$ kubectl edit replicaset <REPLICA_SET_NAME>

# Check details of ReplicaSets running to see if "replicas" is now the preffered number 
$ kubectl get replicaset
```

```bash
# 2nd Way -- Scale via command-line using the "scale" argument directly, without editing any YAML definition file 
$ kubectl scale --replicas=<NUMBER> replicaset <REPLICA_SET_NAME>
```
