### 1. What is the user used to execute the sleep process within the `ubuntu-sleeper` Pod (in the default namespace)?

*Answer:* The user used to execute the sleep process within the `ubuntu-sleeper` Pod is `root`.

```bash
$ kubectl exec ubuntu-sleeper -- whoami
$ kubectl exec -it ubuntu-sleeper -- ps -aux
```

### 2. Edit the Pod `ubuntu-sleeper` to run the sleep process with user ID `1010`

*Note:* Only make the necessary changes. Do not modify the name or image of the Pod.

*Answer:* We need to edit the Pod named `ubuntu-sleeper` and add:

```bash
secretContext:
      runAsUser: 1010
```

under the `spec -> containers` section. Then use below command to populates the change:

```bash
$ kubectl replace --force -f /tmp/kubectl-edit-3133368033.yaml
```

Verification:

```bash
$ kubectl exec -it ubuntu-sleeper -- ps -aux | grep sleep
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
