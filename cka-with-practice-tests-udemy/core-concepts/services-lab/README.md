### 1. Check the number of Services running on the system along with their details

```bash
$ kubectl get service -o wide

# Short version
$ kubectl get svc -o wide
```

### 2. Check the details of a Service

```bash
$ kubectl describe svc <SERVICE_NAME>
```

### 3. Create a Service from a file 

```bash
$ kubectl create -f <SERVICE_DEFINITION_NAME>.yaml
```

### 4. Check the details of a Service

```bash
$ kubectl describe service <SERVICE_NAME>
```

### 5. Delete a Service

```bash
$ kubectl delete service <SERVICE_NAME>
```
