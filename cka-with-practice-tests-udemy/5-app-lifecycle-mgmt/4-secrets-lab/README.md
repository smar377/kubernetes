### 1. How many Pods exist on the system? In the current (default) namespace

```bash
$ kubectl get pods -n default -o wide
```

### 2. What is the environment variable name set on the container in the pod?

*Answer:* `APP_COLOR`

```bash
$ kubectl describe pod webapp-color | grep -i -A1 environment
```

### 3. What is the value set on the environment variable `APP_COLOR` on the container in the pod?

*Answer:* `pink`

```bash
$ kubectl describe pod webapp-color | grep -i -A1 environment
```

### 4. Update the environment variable on the Pop to display a green background

*Note:* Delete and recreate the Pod. Only make the necessary changes. Do not modify the name of the Pod.

```bash
# 1st Way - Dry-run to export the Pod definition to a YAML file
$ kubectl run webapp-color --image=kodekloud/webapp-color --labels='name=webapp-color' --env='APP_COLOR=green' --dry-run=client -o yaml > webapp-green-pod-env.yaml

# Delete existing Pod
$ kubectl delete pod webapp-color

# Create new webapp-color Pod from YAML file
$ kubectl create -f webapp-green-pod-env.yaml
```

```bash
# 2nd Way - Create new Pod after first deleting the old one directly from command line
$ kubectl run webapp-color --image=kodekloud/webapp-color --labels='name=webapp-color' --env='APP_COLOR=green'
```

### 5. How many `ConfigMaps` exist in the default namespace?

```bash
# 1st Way
$ kubectl get configmaps -n default -o wide

# 2nd Way - Short version
$ kubectl get cm -n default -o wide
```

### 6. Identify the database host from the `ConfigMap` `db-config`

*Answer:* `SQL01.example.com`

```bash
$ kubectl describe configmaps db-config 
```

### 7. Create a new `ConfigMap` for the `webapp-color` Pop (use the spec given)

```bash
# 1st Way - Use dry-run mode to export to a YAML file
$ kubectl create configmap webapp-config-map --from-literal=APP_COLOR=darkblue --dry-run=client -o yaml > webapp-config-map.yaml

# Create the ConfigMap
$ kubectl create -f webapp-config-map.yaml

# Check if deployment was successful
$ kubectl get configmaps -n default -o wide
```

```bash
# 2nd Way - Deploy the ConfigMap directly from the command line
$ kubectl create configmap webapp-config-map --from-literal=APP_COLOR=darkblue
```

### 8. Update the environment variable on the Pod to use the newly created `ConfigMap`

*Note:* Delete and recreate the Pod. Only make the necessary changes. Do not modify the name of the Pod.

I have created a new YAML Pod definition file named `webapp-green-pod-env-from.yaml` and have added as extension the `envFrom` section.

Then delete the old Pod, create the new and check the web page:

```bash
$ kubectl delete pod webapp-color
$ kubectl create -f webapp-green-pod-env-from.yaml
$ kubectl describe pod webapp-color | grep -i -A1 environment
```
