### 1. The cluster is broken. We tried deploying an application but it's not working. Troubleshoot and fix the issue

*Hint:* Start looking at the Deployments.

*Answer:* First things first we check the status of all control plane Pod components:

```bash
$ kubectl get pod -n kube-system -o wide
`````

From above aoutput we see that the scheduler Pod named `kube-controller-controlplane` has an issue as its status is set to `CrashLoopBackOff`.
We check for more details with:

```bash
$ kubectl describe -n kube-system pod kube-scheduler-controlplane
```

and it seems that the error presented is:

***`Error: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "kube-schedulerrrr": executable file not found in $PATH: unknown`***

Verdict is that the command run by the scheduler Pod is incorrect. We need to correct this in its YAML manifest file and re-deploy the scheduler Pod:

```bash
# Check where the error is
$ cat /etc/kubernetes/manifests/kube-scheduler.yaml | grep -A8 spec:

# Fix
$ vi /etc/kubernetes/manifests/kube-scheduler.yaml

# Re-deploy scheduler Pod
$ 
```

### 2. 

*Hint:*

*Answer:*

```bash

`````

### 3. 

*Hint:*

*Answer:*

```bash

`````

### 4. 

*Hint:*

*Answer:*

```bash

`````

### 5. 

*Hint:*

*Answer:*

```bash

`````

### 6. 

*Hint:*

*Answer:*

```bash

`````

### 7. 

*Hint:*

*Answer:*

```bash

`````

### 8. 

*Hint:*

*Answer:*

```bash

`````

### 9. 

*Hint:*

*Answer:*

```bash

`````

### 10. 

*Hint:*

*Answer:*

```bash

`````````
````
````
````
````
````
````
````
````
````
