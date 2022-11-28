### 1. Identify the certificate file used for the `kube-api` server

*Answer:* `/etc/kubernetes/pki/apiserver.crt`

```bash
# 1st Way - Check the description of the kube-apiserver Pod
$ kubectl describe pod kube-apiserver-controlplane -n kube-system | grep '\--tls-cert-file'

# 2nd Way - Check the YAML file of the kube-apiserver static Pod
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep '\--tls-cert-file'
```

### 2. Identify the certificate file used to authenticate `kube-apiserver` as a client to ETCD Server

*Answer:* `/etc/kubernetes/pki/apiserver-etcd-client.crt`

```bash
# 1st Way - Check the description of the kube-apiserver Pod
$ kubectl describe pod kube-apiserver-controlplane -n kube-system | grep '\--etcd-certfile'

# 2nd Way - Check the YAML file of the kube-apiserver static Pod
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep '\--etcd-certfile'
```

### 3. Identify the key used to authenticate `kube-apiserver` to the `kubelet` server

*Answer:* `/etc/kubernetes/pki/apiserver-kubelet-client.key`

```bash
# 1st Way - Check the description of the kube-apiserver Pod
$ kubectl describe pod kube-apiserver-controlplane -n kube-system | grep '\--kubelet-client-key'

# 2nd Way - Check the YAML file of the kube-apiserver static Pod
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep '\--kubelet-client-key'
```

### 4. Identify the ETCD Server certificate used to host ETCD server

*Answer:* `/etc/kubernetes/pki/etcd/server.crt`

```bash
# 1st Way - Check the description of the etcd Pod
$ kubectl describe pod etcd-controlplane -n kube-system | grep '\--cert-file'

# 2nd Way - Check the YAML file of the etcd static Pod
$ cat /etc/kubernetes/manifests/etcd.yaml | grep '\--cert-file'
```

### 5. Identify the ETCD Server CA Root Certificate used to serve ETCD Server

*Hint:* ETCD can have its own CA. So this may be a different CA certificate than the one used by `kube-api` server.

*Answer:* `/etc/kubernetes/pki/etcd/ca.crt`

```bash
# 1st Way - Check the description of the etcd Pod
$ kubectl describe pod etcd-controlplane -n kube-system | grep '\--trusted-ca-file'

# 2nd Way - Check the YAML file of the etcd static Pod
$ cat /etc/kubernetes/manifests/etcd.yaml | grep '\--trusted-ca-file'
```

### 6. What is the Common Name (CN) configured on the Kube API Server certificate?

*Hint:* OpenSSL Syntax -> `openssl x509 -in file-path.crt -text -noout`

*Answer:* `Subject: CN = kube-apiserver`

```bash
$ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout | grep CN
```

### 7. What is the name of the CA who issued the Kube API Server certificate?

*Answer:* `Issuer: CN = kubernetes`

```bash
$ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout | grep -i issuer
```

### 8. Which of the below alternate names is NOT configured on the Kube API Server certificate?

*Answer:* `kube-master`

```bash
$ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
```

### 9. What is the Common Name (CN) configured on the ETCD Server certificate?

*Answer:* `controlplane`

```bash
$ openssl x509 -in /etc/kubernetes/pki/etcd/server.crt -text -noout | grep CN
```

### 10. How long, from the issued date, is the Kube-API Server certificate valid for?

*Hint:* File -> `/etc/kubernetes/pki/apiserver.crt`

*Answer:* **1 year**

```bash
$ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout | grep -E 'Validity|Not\s'
```

### 11. How long, from the issued date, is the Root CA Certificate valid for?

*Hint:* File -> `/etc/kubernetes/pki/ca.crt`

*Answer:* **10 years**

```bash
$ openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout | grep -E 'Validity|Not\s'
```

### 12. `Kubectl` suddenly stops responding to commands

Check it out! Someone recently modified the `/etc/kubernetes/manifests/etcd.yaml` file.

You are asked to investigate and fix the issue. Once you fix the issue wait for sometime for `kubectl` to respond. Check the logs of the ETCD container.

*Answer:* The certificate file used here is incorrect. It is set to `/etc/kubernetes/pki/etcd/server-certificate.crt` which does **NOT** exist. As we saw in the previous questions the correct path should be `/etc/kubernetes/pki/etcd/server.crt`.

Update the YAML file with the correct certificate path and wait for the ETCD pod to be recreated. Wait for the `kube-apiserver` to get to a `Ready` state.

```bash
$ ls -lah /etc/kubernetes/pki/etcd/server* | grep .crt
$ vi /etc/kubernetes/manifests/etcd.yaml
```

### 13. The `kube-api` server stopped again! Check it out. Inspect the `kube-api` server logs and identify the root cause and fix the issue

*Hint:* Run `crictl ps -a` command to identify the `kube-api` server container. Run `crictl logs container-id` command to view the logs.

*Answer:* If we inspect the `kube-apiserver` container on the `controlplane`, we can see that it is frequently exiting: 

```bash
$ crictl ps -a | grep kube-apiserver
```

If we now inspect the logs of this exited container, we would see the following errors:

```bash
$ crictl logs --tail=2 9a84c532a67c5
```

This indicates an issue with the ETCD CA certificate used by the `kube-apiserver`. Correct it to use the file `/etc/kubernetes/pki/etcd/ca.crt`.

Once the YAML file has been saved, wait for the `kube-apiserver` Pod to be `Ready`. This can take a couple of minutes.

```bash
# STEP 1 - Edit /etc/kubernetes/manifests/kube-apiserver.yaml
$ vi /etc/kubernetes/manifests/kube-apiserver.yaml

# Change the ETCD CA certificate path used from "--etcd-cafile=/etc/kubernetes/pki/ca.crt" to "--etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt"
```
