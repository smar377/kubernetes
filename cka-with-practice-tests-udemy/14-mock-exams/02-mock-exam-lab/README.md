### 1. Take a backup of the etcd cluster and save it to `/opt/etcd-backup.db`

*Answer:*

```bash
$ ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /opt/etcd-backup.db
```

### 2. Create a Pod called `redis-storage` with image `redis:alpine` with a Volume of type `emptyDir` that lasts for the life of the Pod

Specs as per below:

- Pod named `redis-storage` created
- Pod `redis-storage` uses Volume type of emptyDir
- Pod `redis-storage` uses `volumeMount` with `mountPath = /data/redis`

*Answer:*

```bash
# Create the requested Pod via imperative way and some basic characteristics
$ kubectl run redis-storage --image=redis:alpine --dry-run=client -o yaml > redis-storage-pod.yaml
```

Then we need to and add volume `emptyDir` in it:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis-storage
  name: redis-storage
spec:
  containers:
  - image: redis:alpine
    name: redis-storage
    volumeMounts:
    - mountPath: /data/redis
      name: temp-volume
  volumes:
  - name: temp-volume
    emptyDir: {}
```

### 3. Create a new Pod called `super-user-pod` with image `busybox:1.28`. Allow the Pod to be able to `set system_time` (SYS_TIME capabilities)

*Hint:* The container should sleep for 4800 seconds.

*Answer:*

```bash

```

### 4. A Pod definition file is created at `/root/CKA/use-pv.yaml`. Make use of this manifest file and mount the persistent volume called `pv-1`. Ensure the Pod is running and the PV is bound

- `mountPath: `/data`
- `persistentVolumeClaim Name: my-pvc`

*Answer:*

```bash

```

### 5. Create a new Deployment called `nginx-deploy`, with image `nginx:1.16` and **1** replica. Next upgrade the Deployment to `version 1.17` using rolling update.

*Answer:* Explore the `--record` option while creating the Deployment while working with the Deployment definition file. Then make use of the `kubectl apply` command to create or update the Deployment.

To create a Deployment definition file `nginx-deploy`:

```bash
$ kubectl create deployment nginx-deploy --image=nginx:1.16 --dry-run=client -o yaml > deploy.yaml
```

To create a resource from definition file and to record:

```bash
$ kubectl apply -f deploy.yaml --record
```

To view the history of deployment nginx-deploy:

```bash
$ kubectl rollout history deployment nginx-deploy
```

To upgrade the image to next given version:

```bash
$ kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
```

To view the history of deployment nginx-deploy:

```bash
$ kubectl rollout history deployment nginx-deploy
```

### 6. Create a new user called `john`. Grant him access to the cluster. John should have permission to create, list, get, update and delete Pods in the `development` namespace. The private key exists in the location: `/root/CKA/john.key` and CSR at `/root/CKA/john.csr`

***Important Note:*** As of kubernetes 1.19, the CertificateSigningRequest object expects a signerName

*Hint:* Please refer the documentation to see an example. The documentation tab is available at the top right of terminal.

*Answer:* 


### 7. Create a NGINX Pod called `nginx-resolver` using image `nginx`, expose it internally with a service called `nginx-resolver-service`. Test that you are able to look up the service and Pod names from within the cluster. Use the image `busybox:1.28` for DNS lookup. Record results in `/root/CKA/nginx.svc` and `/root/CKA/nginx.pod`

*Hint:*

*Answer:*

```bash

```

### 8. Create a static Pod on `node01` called `nginx-critical` with image `nginx` and make sure that it is recreated/restarted automatically in case of a failure.

*Hint:* Use `/etc/kubernetes/manifests` as the Static Pod path for example.

*Answer:*

```bash

```
