### 1. Create a secret

```bash
$ kubectl create secret generic my-secret --from-literal=key1=supersecret
$ kubectl get secret my-secret -o yaml
$ kubectl describe secret my-secret
```

### 2. Encode / Decode the secret key

```bash
$ echo -n "supersecret" | base64
c3VwZXJzZWNyZXQ=

$ echo -n "c3VwZXJzZWNyZXQ=" | base64 --decode
supersecret
```

### 3. Check if data in `etcd` is encrypted

```bash
$ ETCDCTL_API=3 etcdctl \
   --cacert=/etc/kubernetes/pki/etcd/ca.crt   \
   --cert=/etc/kubernetes/pki/etcd/server.crt \
   --key=/etc/kubernetes/pki/etcd/server.key  \
   get /registry/secrets/default/my-secret | hexdump -C
```

After running above command, we see the `supersecret` string which means that data in `etcd` is stored in an unencrypted form.

### 4. Determining whether encryption at rest is already enabled

```bash
# Check if kube-apiserver process has "--encryption-provider-config" flag ON
$ ps -aux | grep kube-api | grep "encryption-provider-config"

# We can check also the "kube-apiserver.yaml" file
$ cat /etc/kubernetes/manifest/kube-apiserver.yaml 
```

### 5. Configuring the encryption at rest file (`encryption.yaml`)

```bash
# First generate a 32-byte random key and base64 encode it:
$ $ head -c 32 /dev/urandom | base64
```

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: 5b/9GcjSV2qG7Qsw3q75xQWNirUlM/YLcHuSy5ClTuM=
      - identity: {}
```

### 6. Create encryption path and move `encryption.yaml` there

```bash
$ mkdir /etc/kubernetes/enc
$ mv encryption.yaml /etc/kubernetes/enc
```


### 7. Edit the `kub-apiserver/yaml` manifest file

```bash
# Edit the kube-apiserver.yaml manifest file
$ vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Add parameter under commands in containers section:

- `- --encryption-provider-config=/etc/kubernetes/enc/encryption.yaml`

Also, under `volumeMounts` (inside the Pod), add:

```bash
[output omitted]
- name: enc
  mountPath: /etc/kubernetes/enc
  readonly: true
[output omitted]
```

Last under `volumes` specify the location:

```bash
[output omitted]
- name: enc
  hostPath:
    path: /etc/kubernetes/enc
    type: DirectoryOrCreate
[output omitted]
```

### 8. Check if `kube-apiserver=controlplane` Pod is again available

```bash
$ crictl pods
$ kubectl get pods
```

### 9. Check if encryption parameter is now enabled

```bash
$ ps -aux | grep kube-api | grep "encryption-provider-config"
```

### 10. Create another secret

```bash
$ kubectl create secret generic my-secret-2 --from-literal=key2=topsecret
$ kubectl get secret my-secret -o yaml
$ kubectl describe secret my-secret
```

### 11. Check once more if data (for `my-secret-2`) in `etcd` is encrypted

```bash
$ ETCDCTL_API=3 etcdctl \
   --cacert=/etc/kubernetes/pki/etcd/ca.crt   \
   --cert=/etc/kubernetes/pki/etcd/server.crt \
   --key=/etc/kubernetes/pki/etcd/server.key  \
   get /registry/secrets/default/my-secret-2 | hexdump -C
```

### 12. Ensure all Secrets are encrypted

Since Secrets are encrypted on write, performing an update on a Secret will encrypt that content.

```bash
$ kubectl get secrets --all-namespaces -o json | kubectl replace -f -
```

The command above reads all Secrets and then updates them to apply server side encryption.
