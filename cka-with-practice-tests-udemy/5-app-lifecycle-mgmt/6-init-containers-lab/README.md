### 1. Identify the Pod that has an `initContainer` configured

*Answer:* The Pod that has an `initContainer` configured is `blue` and the name of the container is `init-service`.

```bash
$ kubectl get pods -n default -o wide
$ kubectl describe pod blue -n default
```

### 2. What is the image used by the `initContainer` on the `blue` Pod?

*Answer:* The image used is `busybox`.

```bash
$ kubectl describe pod blue -n default
```

### 3. What is the state of the `initContainer` on Pod `blue`?

*Answer:* The state of the `initContainer` is `Terminated`.

```bash
$ kubectl describe pod blue -n default
```

### 4. Why is the `initContainer` terminated? What is the reason?

*Answer:* The process completed successfully (`Reason: Completed` under `State`)

### 5. We just created a new app named `purple`. How many `initContainers` does it have?

*Answer:* There are **2** `initContainers` in Pod named `purple`.

```bash
$ kubectl describe pod purple -n default
```

### 6. What is the state of the Pod? 

*Answer:* The state of the Pod is `Pending`.

```bash
$ kubectl describe pod purple -n default | grep -i status
```

### 7. How long after the creation of the Pod will the application come up and be available to users?

*Answer:* Check the commands used in the `initContainers`. The first one (`warm-up-1`) sleeps for **600 seconds (10 minutes)** and the second one (`warm-up-2`) sleeps for **1200 seconds (20 minutes)**. Adding the sleep times for both `initContainers`, the application will start after **1800 seconds or 30 minutes**.

```bash
$ kubectl describe pod purple -n default
```

### 8. Update the Pod `red` to use an `initContainer` that uses the `busybox` image and sleeps for 20 seconds

Delete and re-create the pod if necessary. But make sure no other configurations change.

```bash
# STEP 1 - Edit the Pod
$ kubectl edit pod red

# STEP 2 - Add the initContainer configuration and save the file
# STEP 3 - We will get an error that we cannot save it due to permission issues, it's ok we will simply use the temp file to recreate the Pod

# STEP 4 - Recreate the red Pod using the temp file
$ kubectl replace --force -f /tmp/kubectl-edit-3572383452.yaml
```
