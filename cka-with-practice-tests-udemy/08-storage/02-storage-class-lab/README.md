### 1. How many StorageClasses exist in the cluster right now?

*Answer:*  There is only **1** StorageClass configured in the cluster named `local-path (default)`.

```bash
$ kubectl get storageclasses -n default
$ kubectl get sc -n default
```

### 2. How about now? How many StorageClasses exist in the cluster?

*Hint:* We just created a few new StorageClasses. Inspect them.

*Answer:* Now it seems to be **3** StorageClasses in the cluster. The two new ones are:

- `local-storage`
- `protworx-io-priority-high`

```bash
$ kubectl get storageclasses -n default
$ kubectl get sc -n default
```

### 3. What is the name of the StorageClass that does not support dynamic volume provisioning?

*Hint:* Look for the StorageClass name that uses `no-provisioner`.

*Answer:* The `local-storage` Storageclass makes use of the `no-provisioner` and currently does not support dynamic provisioning.

From [Kubernetes - StorageClasses - Local](https://kubernetes.io/docs/concepts/storage/storage-classes/#local):

Local volumes do not currently support dynamic provisioning, however a StorageClass should still be created to delay volume binding until Pod scheduling. This is specified by the WaitForFirstConsumer volume binding mode.

Delaying volume binding allows the scheduler to consider all of a Pod's scheduling constraints when choosing an appropriate PersistentVolume for a PersistentVolumeClaim.

```bash
$ kubectl get sc -n default
```

### 4. What is the Volume Binding Mode used for this storage class (the one identified in the previous question)?

*Answer:* The Volume Binding Mode used for `local-storage` StorageClass is `WaitForFirstConsumer`.

```bash
$ kubectl get sc -n default
```

### 5. What is the Provisioner used for the StorageClass called `portworx-io-priority-high`?

*Hint:*

*Answer:* The Provisioner used for the StorageClass called `portworx-io-priority-high` is `portworx-volume`.

```bash
$ kubectl get sc -n default
```

### 6. Is there a PersistentVolumeClaim that is consuming the PersistentVolume called `local-pv`?

*Answer:* There is **NOT**.

```bash
$ kubectl get pvc -n default
```

### 7. Let's fix that. Create a new PersistentVolumeClaim by the name of `local-pvc` that should bind to the volume `local-pv`

*Hint:* Inspect the PersistentVolume `local-pv` and look for the Access Mode, Storage and StorageClassName used. Use this information to create the PVC.

```bash
$ kubectl get pv local-pv -o yaml
$ vi local-pvc.yaml
$ kubectl create -f local-pvc.yaml
```

Verification:

```bash
$ kubectl get pv,pvc -n default
```

### 8. What is the status of the newly created PersistentVolumeClaim?

*Answer:* The status of newly created PVC named `local-pvc` is `Pending`.

```bash
$ kubectl get pv,pvc -n default
```

### 9. Why is the PVC in a `Pending` state despite making a valid request to claim the volume called `local-pv`?

*Hint:* Inspect the PVC events.

*Answer:* The StorageClass used by the PVC uses `WaitForFirstConsumer` volume binding mode. This means that the persistent volume will **NOT** bind to the claim until a Pod makes use of the PVC to request storage.

```bash
$ kubectl describe pvc local-pvc | grep -iA3 events
```

### 10. Create a new Pod called nginx with the image `nginx:alpine`. The Pod should make use of the PVC `local-pvc` and mount the volume at the path `/var/www/html`

*Hint:* The PV local-pv should in a bound state.

*Answer:*

```bash

```
