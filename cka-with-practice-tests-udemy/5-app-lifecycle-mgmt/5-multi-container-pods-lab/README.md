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

### 4. We have deployed an application logging stack in the `elastic-stack namespace`

Please inspect it. Before proceeding with the next set of questions, please wait for all the pods in the `elastic-stack` namespace to be ready. This can take a few minutes. Let us check this by running:

```bash
$ kubectl get all -n elastic-stack -o wide
```

### 5. Once the Pod is in a ready state, inspect the Kibana UI using the link above your terminal

There shouldn't be any logs for now. We will configure a `sidecar` container for the application to send logs to Elastic Search.

*Note:* It can take a couple of minutes for the Kibana UI to be ready after the Kibana Pod is ready.

Inspect the Kibana logs by running:

```bash
$ kubectl -n elastic-stack logs kibana
```

### 6. Inspect the `app` Pod and identify the number of containers in it

It is deployed in the elastic-stack namespace.

*Answer:* The `app` Pod contains only **1** container named `app` with `image=kodekloud/event-simulator`

```bash
$ kubectl get pod app -n elastic-stack -o wide
$ kubectl describe pod app -n elastic-stack
```

### 7. The application outputs logs to the file `/log/app.log`

View the logs and try to identify the user having issues with `Login`. Inspect the log file inside the Pod.

For this, we need to `exec` in to the container and inspect the logs stored in `/log/app.log` path:

```bash
$ kubectl -n elastic-stack exec -it app -- cat /log/app.log
```

*Answer:* `USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.`

Another way to check the logs for this is to run below command:

```bash
$ kubectl logs app -n elastick-stack
```

### 8. Edit the pod to add a sidecar container to send logs to Elastic Search. Mount the log volume to the sidecar container

Only add a new container. Do not modify anything else. Use the spec provided below.

*Note:* State persistence concepts are discussed in detail later in this course. For now please make use of the below documentation link for updating the concerning pod:

- [Communicate Between Containers in the Same Pod Using a Shared Volume](https://kubernetes.io/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/)

```bash
# STEP 1 - Create new YAML definition file

# STEEP 2 - Delete the old Pod
$ kubectl delete pod app -n elastic-stack

# STEP 3 - Create new Pod
$ kubectl create -f multi-container-pod-sidecar.yaml

# STEP 4 - Check is the deployment was successful
$ kubectl get pods -n elastic-stack
```

### 9. Inspect the Kibana UI

You should now see logs appearing in the `Discover` section. You might have to wait for a couple of minutes for the logs to populate. You might have to create an index pattern to list the logs.

If not sure check this video: [Kubernetes CKAD - Kibana Dashboard](https://bit.ly/2EXYdHf)
