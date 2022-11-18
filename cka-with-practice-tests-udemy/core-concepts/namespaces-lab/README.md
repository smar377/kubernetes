### 1. Check the number of Namespaces running on the system

```bash
$ kubectl get namespaces

# Short version
$ kubectl get ns
```

### 2. Check the number of Pods running in a specific Namespace

```bash
# 1st Way
$ kubectl get pods --namespace=<NAMESPACE_NAME>

# 2nd Way
$ kubectl get pods -n=<NAMESPACE_NAME>
```

### 3. Create a Pod in a specific Namespace 

```bash
$ kubectl run <POD_NAME> --image=<IMAGE_NAME> -n <NAMESPACE_NAME>
```

### 4. Check all Pods running in all Namespaces

```bash
# 1st Way
$ kubectl get pods --all-namespaces

# 2nd Way
$ kubectl get pods -A
```

### 5. Delete a Namespace

```bash
$ kubectl delete namespaces <NAMESPACE_NAME>
```

### 6. Change the default pointing Namespace

```bash
$ kubectl config set-context $(kubectl config current-context) --namespace=<NAMESPACE_NAME>
```

### 7. Access between different Services in the context of Namespaces

1. The DNS name to be used from an application running on a Pod, say `blue`, to access a Service, say a database one named `db-service` in its own namespace, say `marketing` would be:

```bash
http://db-service:6379
```

2. The DNS name to be used from an application running on a Pod, say `blue`, to access a Service, say a database one named `db-service` in another namespace, say `dev` would be:

```bash
http://db-service.dev.svc.cluster.local:6379
```
