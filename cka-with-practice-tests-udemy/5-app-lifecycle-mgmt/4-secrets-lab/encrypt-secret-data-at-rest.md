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

### 5. Configuring the encryption at rest file (encryption.yaml)

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
              secret: <BASE 64 ENCODED SECRET>
      - identity: {}
```

### 6. 

```bash

```
