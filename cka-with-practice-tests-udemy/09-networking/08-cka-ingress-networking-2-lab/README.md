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

### 3. The NGINX Ingress Controller requires a ServiceAccount. Create a ServiceAccount in the `ingress-space` namespace. Use the spec provided below

- Name: `ingress-serviceaccount`

*Answer:* We will do it via the *imperative way* as it is faster:

```bash
# Create the requested serviceAccount in the ingress-space namespace
$ kubectl create serviceaccount ingress-serviceaccount -n ingress-space

# Check if account was created successfully
$ kubectl get sa -n ingress-space
```

### 4. We have created the Roles and RoleBindings for the ServiceAccount. Check it out!!

We inspect the created objects by running the following commands:

```bash
$ kubectl get role,rolebindings -n ingress-space
$ kubectl describe role ingress-role -n ingress-space
$ kubectl describe rolebinding ingress-role-binding -n ingress-space
```

### 5. Let us now deploy the Ingress Controller. Create a Deployment using the file given. The Deployment configuration is given at `/root/ingress-controller.yaml`. There are several issues with it. Try to fix them

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
