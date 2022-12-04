***Important:*** We have deployed two applications. Explore the setup. They are in a different namespaces.

```bash
$ kubectl get deploy -A -o wide
```

### 1. Let us now deploy an Ingress Controller. First, create a namespace called `ingress-space`. We will isolate all ingress related objects into its own namespace

```bash
$ kubectl create namespace ingress-space
$ kubectl get namespaces
```

### 2. The NGINX Ingress Controller requires a ConfigMap object. Create a ConfigMap object in the `ingress-space`. Use the spec given below. No data needs to be configured in the ConfigMap

- Name: `nginx-configuration`

*Answer:* We will do this via **2** different ways.

**1st WAY** - Build a YAML manifest definition file and then create the ConfigMap (*declarative way*):

```bash
$ vi configmap-nginx.yaml
```

```yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configuration
  namespace: ingress-space
```

```bash
# Create the ConfigMap in namespace ingress-space
$ kubectl create -f configmap-nginx.yaml

# Check if creation was successfull
$ kubectl get configmaps -n ingress-space
```

**2nd WAY** - Directly from the command line (*imperative way*)

```bash
# Create the ConfigMap in namespace ingress-space
$ kubectl create configmap nginx-configuration --namespace ingress-space

# Check if creation was successfull
$ kubectl get configmaps -n ingress-space
```

### 3. 

*Hint:*

*Answer:*

```bash

```

### 4. 

*Hint:*

*Answer:*

```bash

```

### 5. 

*Hint:*

*Answer:*

```bash

```

### 6. 

*Hint:*

*Answer:*

```bash

```

### 7. 

*Hint:*

*Answer:*

```bash

```

### 8. 

*Hint:*

*Answer:*

```bash

```

### 9. 

*Hint:*

*Answer:*

```bash

```

### 10. 

*Hint:*

*Answer:*

```bash

```
