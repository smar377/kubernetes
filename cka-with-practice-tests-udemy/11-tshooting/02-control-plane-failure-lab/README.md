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
```

After editing the manifest file `kube-scheduler` Pod will be created and problem will be solved.

Vaerification:

```bash
$ kubectl get pod -n kube-system -o wide
$ kubectl get deploy,pod -n default -o wide
```

### 2. Scale the Deployment `app` to 2 Pods

*Answer:* In order to scale the Deployment `app` we need to issue below command:

```bash
$ kubectl scale deploy app --replicas=2
`````

### 3. Even though the Deployment was scaled to 2, the number of Pods does **NOT** seem to increase. Investigate and fix the issue

*Hint:* Inspect the component responsible for managing `deployments` and `replicasets`. Check the status of all control plane components and identify the component's Pod which has an issue.

*Answer:* First we need to check again the statuses of all Kubernetes Pod components: 

```bash
$ kubectl get pod -n kube-system -o wide
```

We see that `kube-controller-manager-controlplane` this time has an issue and it's in `CrashLoopBackOff` state. I will check the manifest file to see if there are any errors in it. After investigating it it seems that the ``--kubeconfig` points to an incorrect path. It points to `/etc/kubernetes/controller-manager-XXXX.conf` instead of `- --kubeconfig=/etc/kubernetes/controller-manager.conf`.
 
 Let's fix that and haved the Pod recreated:

```bash
$ vi /etc/kubernetes/manifests/kube-controller-manager.yaml
`````

After fixing this we see that the `kube-controller-manager-controlplane` Pod is recreated and this time the `app` Deployment gets its **2** Pods as it should:

```bash
$ kubectl get pod -n kube-system --watch
$ kubectl get deploy,pod -n default -o wide
```

### 4. Something is wrong with scaling again. We just tried scaling the Deployment to 3 `replicas`. But it's **NOT** happening. Investigate and fix the issue

*Answer:* We start again by checking the status of all Kubernetes Pod components:

```bash
$ kubectl get pod -n kube-system -o wide
`````

We see once more that it seems to be an issue with the `kube-controller-manager-controlplane` as its status is set to `CrashLoopBackOff`. Let's investigate the issue by checking the description of the Pod:

```bash
$ kubectl describe -n kube-system pod kube-controller-manager-controlplane
```

From description we did not get much information about the error, let's check also the logs of the Pod:

```bash
$ kubectl logs -n kube-system pods/kube-controller-manager-controlplane
```

This output seems to be more enlightening as it gives us a more clear overview of the error and we have a new lead to search for:

```bash
I1206 09:17:57.597828       1 serving.go:348] Generated self-signed cert in-memory
unable to load client CA provider: open /etc/kubernetes/pki/ca.crt: no such file or directory
```

It seems to be an issue with the client CA certificate path. It appears the path `/etc/kubernetes/pki` is **NOT** mounted from the `controlplane` to the `kube-controller-manager` Pod. If we inspect the Pod manifest file, we can see that the incorrect `hostPath` is used for the volume:

***WRONG PATH:***

```bash
- hostPath:
      path: /etc/kubernetes/WRONG-PKI-DIRECTORY
      type: DirectoryOrCreate
```

***CORRECT PATH:***

```bash
- hostPath: 
    path: /etc/kubernetes/pki 
    type: DirectoryOrCreate
```

Last, check if everything now is correct:

```bash
$ kubectl get pod -n kube-system --watch
$ kubectl get deploy,pod
```
