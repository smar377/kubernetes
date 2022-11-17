### 1. Check the number of Pods (and the image used to deployed) existing on the system

```bash
$ kubectl get pods -o wide
```

### 2. Check the details of a Pod deployment

```bash
$ kubectl describe pod <POD_NAME>
```

### 3. Check the number of ReplicaSets that exist on the system

```bash
$ kubectl get replicaset -o wide
```

### 4. Deploy a ReplicaSet from a YAML file

```bash
# 1st Way
$ kubectl create -f <REPLICA_SET_DEFINITION.yml>

# 2nd Way
$ kubectl apply -f <REPLICA_SET_DEFINITION.yml>
```
