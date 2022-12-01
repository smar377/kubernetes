### 1. We have deployed a Pod. The application stores logs at location `/log/app.log`. View the logs

```bash
$ kubectl get pod -n default
$ kubectl exec -it webapp -- cat /log/app.log
```

### 2. If the Pod was to get deleted now, would you be able to view these logs.

*Answer:* After running below commands to verify and inspect I do not see any PVs / PVCs configured related to Pod `webapp`, meaning that if we delete it all its logs will be deleted as well. The logs are stored in the container's file system that lives only as long as the dontainer does. Once the Pod is destroyed, we cannot view the logs again.

```bash
$ kubectl describe pod webapp -n default
$ kubectl get pv,pvc -n default
```

### 3. Configure a volume to store these logs at `/var/log/webapp` on the host

Use the spec provided below:

- Name: `webapp`
- Image Name: `kodekloud/event-simulator
- Volume HostPath: `/var/log/webapp`
- Volume Mount: `/log`

*Answer:* We will use two different methods to solve this task:

**1st WAY** - Extract Pod definition to a YAML manifest file, edit and then recreate it

```bash
$ kubectl get pod webapp -n default -o yaml > webapp-pod-hostpath-1.yaml
```

Then, we need to edit and save the YAML file and configure a volume as per instructions on the host (please inspect file `webapp-pod-hostpath-1.yaml` for this).

Last but not least, we recreate the file as per below:

```bash
$ kubectl replace -f webapp-pod-hostpath-1.yaml --force
```

**2nd WAY** - Build a Pod definition YAML manifest file from scratch (using a template) and use it to create the Pod later 

```bash
$ vi webapp-pod-hostpath-2.yaml
$ kubectl create -f webapp-pod-hostpath-2.yaml
```

### 4. Create a Persistent Volume with the given specification

Use the spec provided below:

- Volume Name: `pv-log`
- Storage: `100Mi`
- Access Modes: `ReadWriteMany`
- Host Path: `/pv/log`
- Reclaim Policy: `Retain`

*Answer:* We will create a YAML manifest file named `pv-log.yaml` and then use it to create the PersistentVolume:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  persistentVolumeReclaimPolicy: Retain
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 100Mi
  hostPath:
    path: /pv/log
```

```bash
$ kubectl create -f pv-log.yaml
```

Verification:

```bash
$ kubect  get pv -n default
$ kubectl describe pv pv-log -n default
```

### 5. Let us claim some of that storage for our application. Create a Persistent Volume Claim with the given specification

- Volume Name: claim-log-1
- Storage Request: 50Mi
- Access Modes: ReadWriteOnce

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
