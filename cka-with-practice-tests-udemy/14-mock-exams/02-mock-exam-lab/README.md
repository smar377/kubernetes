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
    - mountPath: "/data/redis"
      name: temp-volume
  volumes:
  - name: temp-volume
    emptyDir: {}
```

Last we need to create the Pod by issuing:

```bash
$ kubectl create -f redis-storage-pod.yaml
```

Verification:

```bash
$ kubectl get pod redis-storage
$ kubectl describe pod redis-storage
```

### 3. Create a new Pod called `super-user-pod` with image `busybox:1.28`. Allow the Pod to be able to `set system_time` (SYS_TIME capabilities)

*Hint:* The container should sleep for 4800 seconds.

*Answer:* We will create a YAML Pod definition file with described specifications as per below:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: super-user-pod
spec:
  containers:
  - name: super-user-pod
    image: busybox:1.28
    command: ["sleep", "4800"]
    securityContext:
      capabilities:
        add: ["SYS_TIME"]
```

and then create the Pod by issuing:

```bash
$ kubectl create -f super-user-pod.yaml
```

Verification:

```bash
$ kubectl get pod super-user-pod -o wide
$ kubectl describe pod super-user-pod
$ kubectl exec -it super-user-pod -- sh
  # cd /proc/1
  # cat status
```

### 4. A Pod definition file is created at `/root/CKA/use-pv.yaml`. Make use of this manifest file and mount the persistent volume called `pv-1`. Ensure the Pod is running and the PV is bound

- `mountPath: /data`
- `persistentVolumeClaim Name: my-pvc`

*Answer:* We edit the specificed Pod definition file as per below:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: use-pv
  name: use-pv
spec:
  containers:
  - image: nginx
    name: use-pv
    resources: {}
    volumeMounts:
    - mountPath: "/data"
      name: my-pv
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
  - name: my-pv
    persistentVolumeClaim:
      claimName: my-pvc
status: {}
```

Then we deploy the Pod by issuing:

```bash
$ kubectl create -f /root/CKA/use-pv.yaml 
```

We need also to deploy PVC `my-pvc`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
```

```bash
$ kubectl create -f /root/CKA/my-pvc.yaml
```

Verification:

```bash
$ kubectl get pod use-pv -o wide
$ kubectl describe pod use-pv
$ kubectl get pod,pv,pvc -o wide
```

### 5. Create a new Deployment called `nginx-deploy`, with image `nginx:1.16` and **1** replica. Next upgrade the Deployment to `version 1.17` using rolling update.

*Answer:* Explore the `--record` option while creating the Deployment while working with the Deployment definition file. Then make use of the `kubectl apply` command to create or update the Deployment.

To create a Deployment definition file `nginx-deploy`:

```bash
$ kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1 --dry-run=client -o yaml > nginx-deploy.yaml
```

To create a resource from definition file and to record:

```bash
$ kubectl apply -f nginx-deploy.yaml --record
```

To view the history of Deployment `nginx-deploy`:

```bash
$ kubectl rollout history deployment nginx-deploy
```

To upgrade the image to next given version:

```bash
$ kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
```

To view the history of Deployment `nginx-deploy`:

```bash
$ kubectl rollout history deployment nginx-deploy
```

### 6. Create a new user called `john`. Grant him access to the cluster. John should have permission to create, list, get, update and delete Pods in the `development` namespace. The private key exists in the location: `/root/CKA/john.key` and CSR at `/root/CKA/john.csr`

***Important Note:*** As of kubernetes 1.19, the `CertificateSigningRequest` object expects a `signerName`.

*Hint:* Please refer the documentation to see an example. The documentation tab is available at the top right of terminal.

*Answer:* Solution manifest file to create a CSR as follows:

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: john-developer
spec:
  signerName: kubernetes.io/kube-apiserver-client
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbTlvYmpDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUt2Um1tQ0h2ZjBrTHNldlF3aWVKSzcrVVdRck04ZGtkdzkyYUJTdG1uUVNhMGFPCjV3c3cwbVZyNkNjcEJFRmVreHk5NUVydkgyTHhqQTNiSHVsTVVub2ZkUU9rbjYra1NNY2o3TzdWYlBld2k2OEIKa3JoM2prRFNuZGFvV1NPWXBKOFg1WUZ5c2ZvNUpxby82YU92czFGcEc3bm5SMG1JYWpySTlNVVFEdTVncGw4bgpjakY0TG4vQ3NEb3o3QXNadEgwcVpwc0dXYVpURTBKOWNrQmswZWhiV2tMeDJUK3pEYzlmaDVIMjZsSE4zbHM4CktiSlRuSnY3WDFsNndCeTN5WUFUSXRNclpUR28wZ2c1QS9uREZ4SXdHcXNlMTdLZDRaa1k3RDJIZ3R4UytkMEMKMTNBeHNVdzQyWVZ6ZzhkYXJzVGRMZzcxQ2NaanRxdS9YSmlyQmxVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ1VKTnNMelBKczB2czlGTTVpUzJ0akMyaVYvdXptcmwxTGNUTStsbXpSODNsS09uL0NoMTZlClNLNHplRlFtbGF0c0hCOGZBU2ZhQnRaOUJ2UnVlMUZnbHk1b2VuTk5LaW9FMnc3TUx1a0oyODBWRWFxUjN2SSsKNzRiNnduNkhYclJsYVhaM25VMTFQVTlsT3RBSGxQeDNYVWpCVk5QaGhlUlBmR3p3TTRselZuQW5mNm96bEtxSgpvT3RORStlZ2FYWDdvc3BvZmdWZWVqc25Yd0RjZ05pSFFTbDgzSkljUCtjOVBHMDJtNyt0NmpJU3VoRllTVjZtCmlqblNucHBKZWhFUGxPMkFNcmJzU0VpaFB1N294Wm9iZDFtdWF4bWtVa0NoSzZLeGV0RjVEdWhRMi80NEMvSDIKOWk1bnpMMlRST3RndGRJZjAveUF5N05COHlOY3FPR0QKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  usages:
  - digital signature
  - key encipherment
  - client auth
```

To create and approve this certificate we run:

```bash
$ kubectl create -f john-developer-csr.yaml
$ kubectl get csr
$ kubectl certificate approve john-developer
```

***(!) ATTENTION:*** In order to add the CSR in the CSR object definition file we need first to get the BASE64 format of it. To get that we do:

```bash
$ cat /root/CKA/john.csr | base64 | tr -d "\n"
```

Next, we need to create a Role `developer` and RoleBinding `developer-role-binding`, so we run:

```bash
# Create Role and RoleBinding objects
$ kubectl create role developer --resource=pods --verb=create,list,get,update,delete --namespace=development
$ kubectl create rolebinding developer-role-binding --role=developer --user=john --namespace=development

# Check if objects created correctly
$ kubectl get roles,rolebindings -n development
$ kubectl describe roles developer -n development
$ kubectl describe rolebindings developer-role-binding -n development
```

To verify the permission from `kubectl` utility tool:

```bash
$ kubectl auth can-i create pods --as=john --namespace=development
$ kubectl auth can-i update pods --as=john --namespace=development
$ kubectl auth can-i get pods --as=john --namespace=development
```

### 7. Create a NGINX Pod called `nginx-resolver` using image `nginx`, expose it internally with a Service called `nginx-resolver-service`. Test that you are able to look up the Service and Pod names from within the cluster. Use the image `busybox:1.28` for DNS lookup. Record results in `/root/CKA/nginx.svc` and `/root/CKA/nginx.pod`

*Answer:* First we create a NGINX Pod called `nginx-resolver` using image `nginx`:

```bash
$ kubectl run nginx-resolver --image=nginx
```

Then we create the Service named `nginx-resolver-service` by issuing:

```bash
$ kubectl expose pod nginx-resolver --name=nginx-resolver-service --port=80 --target-port=80 --type=ClusterIP
```

For the testing we will use a Pod named `test-nslookup` via leveraging the `run` flag of `kubectl` tool. Let's look up the Service and Pod names from within the cluster:

```bash
$ kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service
$ kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service > /root/CKA/nginx.svc
```

Get the IP of the `nginx-resolver` Pod and replace the dots (.) with hyphens (-):

```bash
$ kubectl get pod nginx-resolver -o wide
$ kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup 10-50-192-1.default.pod > /root/CKA/nginx.pod
```

### 8. Create a static Pod on `node01` called `nginx-critical` with image `nginx` and make sure that it is recreated/restarted automatically in case of a failure.

*Hint:* Use `/etc/kubernetes/manifests` as the Static Pod path for example.

*Answer:* To create a static Pod called `nginx-critical` use below command:

```bash
$ kubectl run nginx-critical --image=nginx --dry-run=client -o yaml > nginx-critical-pod.yaml
```

Copy the contents of this file or use SCP command to transfer this file from `controlplane` to `node01` node:

```bash
$ root@controlplane:~# scp nginx-critical-pod.yaml node01:/root/
```

To know the IP Address of the `node01` node:

```bash
root@controlplane:~# kubectl get nodes -o wide
root@controlplane:~# ssh node01
OR
root@controlplane:~# ssh 10.62.171.3
```

On `node01` node:

Check if static Pod directory is present which is `/etc/kubernetes/manifests`, if it's not present then create it.

```bash
root@node01:~# mkdir -p /etc/kubernetes/manifests
```

Add that complete path to the `staticPodPath` field in the `kubelet` `config.yaml` file:

```bash
root@node01:~# vi /var/lib/kubelet/config.yaml
```

Now, move the `nginx-critical-pod.yaml` to path `/etc/kubernetes/manifests/`:

```bash
root@node01:~# mv /root/nginx-critical-pod.yaml /etc/kubernetes/manifests/
```

Go back to the `controlplane` node and check the status of static Pod:

```bash
root@node01:~# exit
root@controlplane:~# kubectl get pods
```
