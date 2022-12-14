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

Use the specifications provided below:

- Name: `webapp`
- Image Name: `kodekloud/event-simulator`
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

### 4. Create a PersistentVolume with the given specifications

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

### 5. Let us claim some of that storage for our application. Create a PersistentVolumeClaim with the given specification

- Volume Name: claim-log-1
- Storage Request: 50Mi
- Access Modes: ReadWriteOnce

*Answer:* Solution includes build a YAML manifest file to create a PVC named `claim-log-1` with given properties:

```yaml
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
```

```bash
$ kubectl create -f claim-log-1.yaml
```

Verification:

```bash
$ kubectl get pvc -n default
$ kubectl describe pvc claim-log-1 -n default
```

### 6. What is the state of the PersistentVolumeClaim?

*Answer:* The status of PVC named `claim-log-1` is `Pending`.

```bash
$ kubectl get pvc -n default
```

### 7. What is the state of the PersistentVolume?

*Answer:* The status of PV named `pv-log` is `Available`.

```bash
$ kubectl get pv -n default
```

### 8. Why is the claim not bound to the available PersistentVolume?

*Answer:* After running below commands and looking at the Access Modes we figure out that they do **NOT** match and this is preventing the PVC from binding to PV.

```bash
$ kubectl get pv,pvc -n default
```

### 9. Update the `accessModes` on the claim to bind it to the PV

*Hint:* Delete and recreate the PVC `claim-log-1`.

*Answer:* Change `accessModes` in PVC manifest file from `ReadWriteOnce` to `ReadWriteMany` and then I recreate the PVC:

```bash
$ kubectl replace --force -f claim-log-1.yaml
```

### 10. You have requested `50Mi`, how much capacity is now available on the PVC?

*Answer:* The capacity available on the PVC is `100Mi`.

```bash
$ kubectl get pvc claim-log-1
```

### 11. Update the `webapp` Pod to use the PersistentVolumeClaim as its storage

*Answer:* Replace `hostPath` configured earlier with the newly created `PersistentVolumeClaim`. For this I have created a new manifest file called `webapp-pod-pvc.yaml`. Please check it to see what has been changed to facilitate the requirements of this task.

```bash
$ kubectl delete pod webapp -n default
$ kubectl create -f webapp-pod-pvc.yaml
```

### 12. What is the Reclaim Policy set on the PersistentVolume `pv-log`?

*Answer:* The Reclaim Policy set on the Persistent Volume `pv-log` is set to `Retain`.

```bash
$ kubectl describe pv -n default | grep -i reclaim
```

### 13. What would happen to the PV if the PVC was destroyed?

*Answer:* The PV is **NOT** deleted but it will be unavailable. This is because the reclaim policy is set to `Retain` which basically means that it needs to be deleted manually by admin, but in the meantime will remain unavailable from other PVC to be claimed.

### 14. Try deleting the PVC and notice what happens

*Hint*: If the command hangs, you can use `CTRL+C` to get back to the bash prompt **OR** check the status of the PVC from another terminal.

*Answer:* The PVC is stuck in `Terminating` state.

```bash
$ kubectl delete pvc claim-log-1 -n default
$ kubectl get pvc -n default
```

### 15. Why is the PVC stuck in `Terminating` state?

*Answer:* The PVC was still being used by the `webapp` Pod when we issued the `delete` command. Until the Pod is deleted, the PVC will remain in the `Terminating` state.

### 16. Let us now delete the `webapp` Pod

*Hint*: Once deleted, wait for the Pod to fully terminate.

```bash
$ kubectl delete pod webapp -n default
```

### 17. What is the state of the PVC now?

*Answer:* It has been deleted.

```bash
$ kubectl get pvc -n default
```

### 18. What is the state of the PersistentVolume now?

*Answer:* The status of the PV `pv-log` is set to `Released`.

```bash
$ kubectl get pv,pvc,pod -n default
```
