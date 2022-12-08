### 1. Get the list of nodes in JSON format and store it in a file at `/opt/outputs/nodes.json`

*Answer:* We need to use the following command:

```bash
$ kubectl get nodes -o json > /opt/outputs/nodes.json
`````

### 2. Get the details of the node `node01` in JSON format and store it in file `/opt/outputs/node01.json`

*Answer:* Below command should be used:

```bash
$ kubectl get nodes node01 -o json > /opt/outputs/node01.json
`````

### 3. Use JSON PATH query to fetch the node names and store them in `/opt/outputs/node_names.txt`

*Hint:* Remember the file should only have node names.

*Answer:* We need to use the following commad:

```bash
$ kubectl get nodes -o=jsonpath='{.items[*].metadata.name}' > /opt/outputs/node_names.txt
`````

### 4. Use JSON PATH query to retrieve the `osImages` of all the nodes and store it in file `/opt/outputs/nodes_os.txt`

*Hint:* The `osImages` are under the `nodeInfo` section under `status` of each node.

*Answer:* Command to get the requested data:

```bash
$ kubectl get nodes -o jsonpath='{.items[*].status.nodeInfo.osImage}' > /opt/outputs/nodes_os.txt
`````

### 5. A `kube-config` file is present at `/root/my-kube-config`. Get the user names from it and store it in a file `/opt/outputs/users.txt`

*Hint:* Use the command `kubectl config view --kubeconfig=/root/my-kube-config` to view the custom `kube-config`.

*Answer:* We should use below command:

```bash
$ kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.users[*].name}" > /opt/outputs/users.txt
`````

### 6. A set of PersistentVolumes are available. Sort them based on their capacity and store the result in file `/opt/outputs/storage-capacity-sorted.txt`

*Answer:* We should use the following command:

```bash
$ kubectl get pv --sort-by=.spec.capacity.storage > /opt/outputs/storage-capacity-sorted.txt
`````

### 7. That was good, but we don't need all the extra details. Retrieve just the first 2 columns of output and store it in `/opt/outputs/pv-and-capacity-sorted.txt`

*Hint:* The columns should be named NAME and CAPACITY. Use the `custom-columns` option and remember, it should still be sorted as in the previous question.

*Answer:* The commadn for this is:

```bash
$ kubectl get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage > /opt/outputs/pv-and-capacity-sorted.txt
`````

### 8. Use a JSON PATH query to identify the context configured for the `aws-user` in the `my-kube-config` context file and store the result in `/opt/outputs/aws-context-name`

*Answer:* Use the following command:

```bash
$ kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.contexts[?(@.context.user=='aws-user')].name}" > /opt/outputs/aws-context-name
`````
