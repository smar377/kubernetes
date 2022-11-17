### 1. Check the number of pods (and the image used to deployed) existing on the system

```bash
$ kubectl get pods -o wide
```

### 2. Check the details of a pod deployment

```bash
$ kubectl describe pod <pod_name>
```

### 3. Check the number of replicasets that exist on the system

```bash
$ kubectl get replicaset -o wide
```


