### 1. We have deployed a few Pods running workloads. Inspect them. Wait for the Pods to be ready before proceeding to the next question

```bash
# 1st Way
$ kubectl get pods --all-namespaces -o wide

# 2nd Way
$ kubectl get pods -A -o wide
```

### 2. Let us deploy `metrics-server` to monitor the Pods and Nodes. Pull the git repository for the deployment files

```bash
$ git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
```

### 3. Deploy the `metrics-server` by creating all the components downloaded 

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
