### 1. Check the number of Namespaces running on the system

```bash
$ kubectl get namespaces
```

### 2. Check the number of Pods running in a specific Namespace

```bash
$ kubectl get pods --namespace=<NAMESPACE_NAME>
```

### 3. Create a Pod in a specific Namespace 

```bash
$ kubectl run <POD_NAME> --image=<IMAGE_NAME> -n <NAMESPACE_NAME>
```

### 4. Check all Pods running in all Namespaces

```bash
$ kubectl get pods --all-namespaces
```

### 5. Delete a Namespace

```bash
$ kubectl delete namespaces <NAMESPACE_NAME>
```

### 6. Change the default pointing Namespace

```bash
$ kubectl config set-context $(kubectl config current-context) --namespace=<NAMESPACE_NAME>
```

