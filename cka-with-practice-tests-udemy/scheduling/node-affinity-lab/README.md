### 1. How many Labels exist on node `node01`?

```bash
$ kubectl describe nodes node01
```

### 2. Apply a label color=blue to node `node01`

```bash
# Apply label
$ kubectl label nodes node01 color=blue

# Check labels on node01
$ kubectl describe nodes node01 | grep -A5 Label
```

### 3. Create a new Deployment named `blue` with the `nginx` image and 3 replicas

```bash
# Create Deployment
$ kubectl create deployment blue --image=nginx --replicas=3

# Check the status of the newly created Deployment
$ kubectl get deploy -o wide
```

### 4. Which nodes can the Pods for the `blue` Deployment be placed on? Make sure to check Taints on both nodes

*Answer:* Both nodes have **NO** taints set on them, so both of them can facilitate Pods pf the `blue` Deployment.

```bash
# Check the Taints on controlplane node
$ kubectl describe nodes controlplane | grep Taint

# Check the Taints on node01 node
$ kubectl describe nodes controlplane | grep Taint
```

### 5. Set Node Affinity to the Deployment to place the Pods on `node01` only

*Please check the `blue-deploy-definition.yaml` file for more information*

### 6. Create a new Deployment named `red` with the `nginx` image and 2 replicas, and ensure it gets placed on the `controlplane` node only. Use the label key `node-role.kubernetes.io/control-plane` which is already set on the `controlplane` node

*Please check the `blue-deploy-definition.yaml` file for more information*

```bash
# Run the red Deployment
$ kubectl create -f red-deploy-definition.yaml

# Check on which node the Pods of red Deployment are running on
$ kubectl get pods -o wide
```

Alternatively we can:

```bash
# Create a Deployment with dry-run and toss it to a YAML file
$ kubectl create deployment red --image=nginx --replicas=2 --dry-run=client -o yaml > red-deply-definition.yaml

# Edit the YAML file and within the spec section add the affinity fields
# and then
# last but not lease, create the Deployment by issuing
$ kubectl create -f red-deploy-definition.yaml
```
