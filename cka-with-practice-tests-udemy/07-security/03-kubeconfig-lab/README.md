### 1. Where is the default `kubeconfig` file located in the current environment?

*Hint:* Find the current home directory by looking at the HOME environment variable.

*Answer:* The default `kubeconfig` file is located in `/root/.kube/config`.

```bash
$ echo $HOME
$ cd /root/.kube
$ cat config
```

### 2. How many clusters are defined in the default `kubeconfig` file?

*Answer:* There is only **1** cluster defined in the default `kubeconfig` file named `kubernetes`.

```bash
$ cat /root/.kube/config
$ kubectl config view
```

### 3. How many users are defined in the default `kubeconfig` file?

*Answer:* There is only **1** user defined in the default `kubeconfig` file named `kubernetes-admin`.

```bash
$ cat /root/.kube/config
$ kubectl config view
```

### 4. How many contexts are defined in the default `kubeconfig` file?

*Answer:* There is only **1** context defined in the default `kubeconfig` file named 'kubernetes-admin@kubernetes'.

```bash
$ cat /root/.kube/config
$ kubectl config view
```

### 5. What is the user configured in the current context?

*Answer:* The user configured in the current context is `kubernetes-admin`.

```bash
$ cat /root/.kube/config
$ kubectl config view
```

### 6. What is the name of the cluster configured in the default `kubeconfig` file?

*Answer:* The named of the cluster configured in the default `kubeconfig` file is called `kubernetes`.

```bash
$ cat /root/.kube/config
$ kubectl config view
```

### 7. A new kubeconfig file named `my-kube-config` is created. It is placed in the `/root` directory. How many clusters are defined in that `kubeconfig`? file?

*Answer:* In this new `kubeconfig` file, there are **4** different clusters configured:

- `development`
- `kubernetes-on-aws`
- `production`
- `test-cluster-1`

```bash
$ cat /root/my-kube-config
$ kubectl config view --kubeconfig my-kube-config
```

### 8. How many contexts are configured in the `my-kube-config` file?

*Answer:* There are **4** different contexts configures in `my-kube-config` file:

- `aws-user@kubernetes-on-aws`
- `research`
- `test-user@development`
- `test-user@production`

```bash
$ cat /root/my-kube-config
$ kubectl config view --kubeconfig my-kube-config
```

### 9. What user is configured in the `research` context?

*Answer:* The `dev-user` is configured in the `research` context.

```bash
$ cat /root/my-kube-config
$ kubectl config view --kubeconfig my-kube-config
```

### 10. What is the name of the client-certificate file configured for the `aws-user`?

*Answer:* The name of the client-certificate file configured for the `aws-user` is `aws-user.crt`.

```bash
$ cat /root/my-kube-config
$ kubectl config view --kubeconfig my-kube-config
```

### 11. What is the current context set to in the `my-kube-config` file?

*Answer:* The currect context set in `my-kube-config` file is `test-user@development`.

```bash
$ kubectl config current-context --kubeconfig my-kube-config
```

### 12. We want to use the `dev-user` to access `test-cluster-1`. Set the current context to the right one

*Hint:* Once the right context is identified, use the `kubectl config use-context` command.

```bash
# To use that context, run the command:
$ kubectl config --kubeconfig=/root/my-kube-config use-context research
```

```bash
# To know the current context, run the command:
$ kubectl config use-context research --kubeconfig my-kube-config
```

### 13. We don't want to have to specify the `kubeconfig` file option on each command. Make the `my-kube-config` file the default `kubeconfig`

*Answer:* In order to do that we simply need to overwrite the content of `/root/.kube/config` file with the content of `my-kube-config`.

```bash
$ mv /root/my-kube-config /root/.kube/config
$ cat /root/.kube/config
$ kubectl config view
```

### 14. With the `current-context` set to `research`, we are trying to access the cluster. However something seems to be wrong. Identify and fix the issue

*Hint:* Try running the `kubectl get pods` command and look for the error. All users certificates are stored at `/etc/kubernetes/pki/users`.

*Answer:* The path to certificate for `dev-user` is incorrect in the `kubeconfig` file. Correct the certificate name which is available at `/etc/kubernetes/pki/users/`. The path needs to be changed from `/etc/kubernetes/pki/users/dev-user/developer-user.crt` to `/etc/kubernetes/pki/users/dev-user/dev-user.crt`

```bash
$ ls -lah /etc/kubernetes/pki/user/dev-user/
$ kubectl config view
```
