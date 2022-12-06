### Troubleshooting Test 1

*Description:* A simple 2 tier application is deployed in the `triton` namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

*Hint:* Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.

*Answer:* After clicking on the `App` tab we see that we get a ***`502 Bad Gateway`*** error.

We change first the default `namespace` to be `triton`, so we don't have tp specify in all our commands the namespace flag:

```bash
$ kubectl config set-context --current --namespace=triton
`````

We check the statuses of all objects created in the `triton` namespace and we see that Pods are stuck in a `ContainerCreating` state:

```bash
$ kubectl get deploy,svc,pod
$ 
```

After checking also the descriptions of the Pods we can more details on the errrors:

```bash
$ kubectl describe pod mysql
$ kubectl describe pod webapp-mysql-776c6655-sdthd
```

***ERROR:***

***`Failed to create pod sandbox: rpc error: code = Unknown desc = [failed to set up sandbox container "424db9a29f12b7989e35ddab60f61d1b95c1c46ee526836682acb0dee3dab59a" network for pod "mysql": networkPlugin cni failed to set up pod "mysql_triton" network: unable to allocate IP address: Post "http://127.0.0.1:6784/ip/424db9a29f12b7989e35ddab60f61d1b95c1c46ee526836682acb0dee3dab59a": dial tcp 127.0.0.1:6784: connect: connection refused, failed to clean up sandbox container "424db9a29f12b7989e35ddab60f61d1b95c1c46ee526836682acb0dee3dab59a" network for pod "mysql": networkPlugin cni failed to teardown pod "mysql_triton" network: Delete "http://127.0.0.1:6784/ip/424db9a29f12b7989e35ddab60f61d1b95c1c46ee526836682acb0dee3dab59a": dial tcp 127.0.0.1:6784: connect: connection refused]`***

We notice also that both services do **NOT** have valid endpoints, which means that something is wrong with the network:

```bash
$ kubectl describe svc mysql | grep -i endpoint
$ kubectl describe svc web-service | grep -i endpoint
```

After checking with below command:

```bash
$ kubectl get ds -n kube-system
```

we notice that the cluster does **NOT** have a network addon installed. We fix that by installing `weave-net` by issuing:

```bash
curl -L https://github.com/weaveworks/weave/releases/download/latest_release/weave-daemonset-k8s-1.11.yaml | kubectl apply -f -
```

Verification and testing:

```bash
$ kubectl get ds -n kube-system
$ kubectl get deploy,svc,pod
```

Finally we see that when now we browse to `App` tab we get a green page!

### 2. 

*Hint:*

*Answer:*

```bash

`````

### 3. 

*Hint:*

*Answer:*

```bash

`````

### 4. 

*Hint:*

*Answer:*

```bash

`````

### 5. 

*Hint:*

*Answer:*

```bash

`````

### 6. 

*Hint:*

*Answer:*

```bash

`````

### 7. 

*Hint:*

*Answer:*

```bash

`````

### 8. 

*Hint:*

*Answer:*

```bash

`````

### 9. 

*Hint:*

*Answer:*

```bash

`````

### 10. 

*Hint:*

*Answer:*

```bash

`````````
````
````
````
````
````
````
````
````
````
