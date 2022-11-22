### 1. We have deployed a number of Pods. They are labelled with `tier`, `env` and `bu`. How many Pods exist in the `dev` environment (`env`)? Use selectors to filter the output

*Note:* We can use the `--no-headers` flag when issuing the `kubectl` command to avoid showing the headers.

```bash
$ kubectl get pods --selector env=dev --no-headers | wc -l
```

### 2. How many Pods are in the finance business unit (`bu`)?

```bash
$ kubectl get pods --selector bu=finance --no-headers | wc -l
```

### 3. How many objects are in the `prod` environment including Pods, ReplicaSets and any other objects?

```bash
# Leveraging the `--no-headers` flag
$ kubectl get all --selector env=prod --no-headers | wc -l

# Alternative way of excluding the headers and the empty lines by leveraging the power of `grep` command
$ kubectl get all --selector env=prod -o wide | egrep -v '^NAME|^$' | wc -l
```

### 4. Identify the Pod which is part of the `prod` environment, the `finance` business unit and of frontend `tier`

```bash
$ kubectl get pods --selector env=prod,bu=finance,tier=frontend
```

### 5. A YAML ReplicaSet definition file is given named `replicaset-definition-1.yaml`. Try to create the ReplicaSet. There is an issue with the file. Try to fix it

*Note:* Issue was that `tier` key value in the `template` section was **NOT** set to `front-end` as it was in the selector's `matchLabels` `tier` key field. 

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
