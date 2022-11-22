### 1. We have deployed a number of Pods. They are labelled with `tier`, `env` and `bu`. How many Pods exist in the `dev` environment (`env`)? Use selectors to filter the output

```bash
$ kubectl get pods --selector env=dev
```

### 2. How many Pods are in the finance business unit (`bu`)?

```bash
$ kubectl get pods --selector bu=finance
```

### 3. How many objects are in the `prod` environment including Pods, ReplicaSets and any other objects?

```bash
$ kubectl get all --selector env=prod -o wide | egrep -v '^NAME|^$' | wc -l
7
```

### 4. Identify the Pod which is part of the `prod` environment, the `finance` business unit and of frontend `tier`

```bash
$ kubectl get pods --selector env=prod,bu=finance,tier=frontend
```

### 5. A ReplicaSet definition file is given named `replicaset-definition-1.yaml`. Try to create the ReplicaSet. There is an issue with the file. Try to fix it

*Note:* Issue was that `tier` key value was **NOT** set to `front-end` as it was in the selector key field. 

```bash
$ cat replicaset-definition-1.yaml 
apiVersion: apps/v1
kind: ReplicaSet
metadata:
   name: replicaset-1
spec:
   replicas: 2
   selector:
      matchLabels:
        tier: front-end
   template:
     metadata:
       labels:
        tier: front-end
     spec:
       containers:
       - name: nginx
         image: nginx
```

```bash
# Create the ReplicaSet
$ kubectl create -f replicaset-definition-1.yaml
```

```bash
# Check the status of the newly created ReplicaSet
$ kubectl get rs -o wide | egrep '^NAME|replicaset'
```
