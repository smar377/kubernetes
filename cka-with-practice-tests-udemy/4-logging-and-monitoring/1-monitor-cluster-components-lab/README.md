### 1. Inspect a few Pods running workloads

```bash
# 1st Way
$ kubectl get pods --all-namespaces -o wide

# 2nd Way
$ kubectl get pods -A -o wide
```

### 2. Deploy `metrics-server` to monitor the Pods and Nodes 

In order to do that, we need to pull the git repository for the deployment files:

```bash
$ git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
```

### 3. Deploy `metrics-server` by creating all the components downloaded 

Run the following command from within the downloaded repository:

```bash
$ kubectl create -f .
```

### 4. Check `metrics server` for gathered data

Run the following commands and check the rest of the questions:

```bash
$ kubectl top node
$ kubectl top pod
```
