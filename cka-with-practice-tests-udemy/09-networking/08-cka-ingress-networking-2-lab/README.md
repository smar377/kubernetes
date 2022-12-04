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

*Answer:* There were **2** errors with the Deployment YAML file. The first was an *indentation* error at line 36 and the second one was a wrong name set for the namespace. It was wrongly set to `ingress-`. After fixing these two errors the Deployment was successful:

```bash
$ kubectl create -f ingress-controller.yaml
$ kubectl get deploy -n ingress-space
```

### 6. Let us now create a service to make Ingress available to external users. Create a service following the given specs

- Name: `ingress`
- Type: `NodePort`
- Port: `80`
- TargetPort: `80`
- NodePort: `30080`
- Namespace: `ingress-space`
- Use the right selector

*Answer:* I have created the YAML manifest file named `ingress-service-external-usage.yaml` as per specifications.

```bash
# Create manifest file for Ingress service
$ vi ingress-service-external-usage.yaml
$ cat ingress-service-external-usage.yaml

# Create the Ingress service for external usage
$ kubectl create -f ingress-service-external-usage.yaml

# Verify that deployment was successfull
$ kubectl get svc -n ingress-space
```

***Note:*** Anothey way (*imperative way*) to implement this, is by leveraging directly the command line and issuing:

```bash
$ kubectl expose -n ingress-space deployment ingress-controller --type=NodePort --port=80 --name=ingress --dry-run=client -o yaml > ingress-service-external-usage.yaml
```

and then manually add the given `nodePort` and `namespace`.

### 7. Create the Ingress Resource to make the applications available at `/wear` and `/watch` on the Ingress service. Create the ingress in the `app-space` namespace.

*Answer:* We will build a YAML manifest file for this and then create the Ingress Resource:

```bash
$ vi ingress.yaml
$ cat ingress.yaml
```

```bash
# Creation of Ingress Resource
$ kubectl create -f ingress.yaml

# Check if creation was successfull
$ kubectl get ingress -n app-space
```

### 8. Access the application using the Ingress tab on top of your terminal. Make sure you can access the right applications at `/wear` and `/watch` paths.

*Answer:* We test by browsing to the following *URLs*:

- https://30080-port-375dff9e3f9745e6.labs.kodekloud.com/wear
- https://30080-port-375dff9e3f9745e6.labs.kodekloud.com/watch
