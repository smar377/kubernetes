***Important:*** In this practice test we will install `weave-net` Pod networking solution to the cluster. Let us first inspect the setup.

### 1. We have deployed an application called `app` in the default `namespace`. What is the state of the pod?

*Answer:* The application is **NOT** running. Try to issue below command gives errors: 

```bash
$ kubelete get pod -n default
```

### 2. Inspect why the Pod is not running.

*Answer:* After running below command we see that the reason behind of the Pod to be created is that **NO** network has been configured.

```bash
$ kubectl describe pod app
```

### 3. Deploy `weave-net` networking solution to the cluster

*Note:* We already have provided a weave manifest file under the /root/weave directory.

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
