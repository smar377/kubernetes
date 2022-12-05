### 1. Install the `kubeadm` and `kubelet` packages on the `controlplane` and `node01`

*Hint:* Use the exact `version 1.24.0-00` for both. Refer to the official Kubernetes documentation:

- https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

and follow the installation steps.

These steps have to be performed on both nodes.


```bash
$ cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF
```

```bash
$ cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```

```bash
$ sudo sysctl --system
```

The container runtime has already been installed on both nodes, so you may skip this step. Next, we need to install `kubeadm`, `kubectl` and `kubelet` on all nodes:

```bash
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates curl
```

```bash
$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

```bash
$ echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

```bash
$ sudo apt-get update
$ sudo apt-get install -y kubelet=1.24.0-00 kubeadm=1.24.0-00 kubectl=1.24.0-00
$ sudo apt-mark hold kubelet kubeadm kubectl
```

### 2. What is the version of `kubelet` installed?

*Answer:* The version of `kubelet` running is `v1.24.0`:

```bash
$ kubelet --version
```

### 3. How many nodes are part of kubernetes cluster currently? Are you able to run `kubectl get nodes`?

*Answer:* No we cannot run the `kubectl get nodes` command and the nodes that are part of Kubernetes cluster is **0**.
This is because the master node is not initialized yet and nodes are not joined to the cluster.

### 4. Lets now bootstrap a kubernetes cluster using kubeadm. The latest version of Kubernetes will be installed

Initialize Control Plane Node (Master Node). Use the following options:

- `apiserver-advertise-address` - Use the IP address allocated to `eth0` on the `controlplane` node
- `apiserver-cert-extra-sans` - Set it to `controlplane`
- `pod-network-cidr` - Set to `10.244.0.0/16`

Once done, set up the `default kubeconfig` file and wait for node to be part of the cluster.

*Answer:* On `controlplane` master node we check the `eth0` interface and write down the IPv4 address as this will be used for the Kube-API server and then we use the `kubeadm` initialization command specifying the parameters given:

```bash
$ ifconfig eth0
$ kubeadm init --apiserver-cert-extra-sans=controlplane --apiserver-advertise-address 10.26.196.6 --pod-network-cidr=10.244.0.0/16
```

After the successful run of above command we get some instructions on how we should proceed on starting the cluster as a normal user (or as a `root` user), but also we are being provided with the command to join the worker node(s) to the cluster:

```bash
[output ommitted]
...
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.26.196.6:6443 --token 2de3ah.2ah0em2ky87k06lo \
        --discovery-token-ca-cert-hash sha256:780ea31fe7438c7fac312f3eef28efede611d64a9b31a810677e82b4b11dc714
```

Since we are a `root` user in this KokeKloud lab environment we run:

```bash
$ export KUBECONFIG=/etc/kubernetes/admin.conf
```

and **NOT** the following which is used to start using the cluster as normal user:

```bash
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Last, we go to the worker node(s) and we apply the following command to join them to the cluster:

```bash
$ kubeadm join 10.26.196.6:6443 --token 2de3ah.2ah0em2ky87k06lo \
        --discovery-token-ca-cert-hash sha256:780ea31fe7438c7fac312f3eef28efede611d64a9b31a810677e82b4b11dc714
```

We can check then on `controlplane` master node:

```bash
$ kubectl get nodes
```

### 5. Install a Network Plugin. As a default, we will go with `flannel`. Refer to the official documentation for the procedure

*Answer:* On the `controlplane` master node run:

```bash
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
