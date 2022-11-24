### 1. Identify the number of containers created in the `red` Pod

Issue below commands and check the `Ready` field under every container

*Answer:* The number of container included in the `red` Pod is **3** (`apple`, `wine` and `scarlet`) 

```bash
$ kubectl get pods red
$ kubectl describe pod red
```

### 2. Identify the name of the containers running in the blue Pod

*Answer:* `teal` and `navy`

```bash
$ kubectl describe pod blue
```

### 3. Create a multi-container Pod with 2 containers

Use the spec given below. If the Pod goes into the `CrashLoopBackOff` then add the command `sleep 1000` in the `lemon` container.

- Name: `yellow`
  - Container 1 Name: `lemon`
  - Container 1 Image: `busybox` 
  - Container 2 Name: `gold`
  - Container 2 Image: `redis`

```bash
# STEP 1 - Use dry-run to create a Pod definition file initially for 1 container and then we will edit it to add the 2nd container
$ kubectl run yellow --image=busybox --labels='name=yellow' --dry-run=client -o yaml > multi-container-pod-2-containers.yaml

# STEP 2 - Edit and add the 2nd container definition

# STEP 3 - Deploy the multi-container Pod
$ kubectl create -f multi-container-pod-2-containers.yaml

# STEP 4- Check the status of the Pod
$ kubectl get pod yellow -o wide
```

The status is `CrashLoopBackOff` so we proceed on the instruction of the question stating that we need to add the command `sleep 1000` in the `lemon` container. For this please check the final form of the `multi-container-pod-2-containers.yaml` file.

Lasst but not least wqe recreate the Pod `lemon` by issuing:

```bash
$ kubectl replace --force -f multi-container-pod-2-containers.yaml
```

### 4. 

*Answer:* `Type` 

### 5. 

```bash

```

```bash

```

### 6. Configure `webapp-pod` to load environment variables from the newly created secret

Delete and recreate the pod if required.

```bash

```
