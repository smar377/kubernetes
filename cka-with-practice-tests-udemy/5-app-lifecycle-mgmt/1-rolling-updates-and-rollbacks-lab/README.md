### 1. We have deployed a simple web application. Inspect the Pods and the Services

```bash
$ kubectl get pods -o wide
$ kubectl get service -o wide
```

### 2. Run the script named `curl-test.sh` to send multiple requests to test the web application (take note of the output)

```bash
$ ./curl-test.sh 
Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK

[output repeats -- ommited]
```

### 3. Inspect the Deployment and identify the number of Pods deployed by it

```bash
$ kubectl get deploy -o wide
$ kubectl get pods -o wide
$ kubectl get all -o wide
```

### 4. What container image is used to deploy the applications?

*Answer:* The image used is `kodekloud/webapp-color:v1`

```bash
$ kubectl describe deploy frontend | grep -i image
```

### 4. What container image is used to deploy the applications?

*Answer:* The image used is `kodekloud/webapp-color:v1`

```bash
$ kubectl describe deploy frontend | grep -i image
```

### 5. Inspect the deployment and identify the current strategy

*Answer:* Strategy type is `RollingUpdate`

```bash
$ kubectl describe deploy frontend | grep -i strategy
```

### 6. Upgrade of application by setting the image on the deployment to `kodekloud/webapp-color:v2`

Do **NOT** delete and re-create the deployment. Only set the new image name for the existing deployment.

```bash
# 1st Way - By editing the Deployment YAML definition file and modifying the image to `kodekloud/webapp-color:v2`
$ kubectl edit deployment frontend
```

```bash
# 2nd Way - By setting the image from command line
$ kubectl set image deploy frontend simple-webapp=kodekloud/webapp-color:v2
```

Let's check now both the Deployment and the Pods:

```bash
$ kubectl get pods,deploy -o wide
```

### 7. Run the script `curl-test.sh` again

Notice the requests now hit both the old and newer versions. However **NONE** of them fail.

```bash
$ ./curl-test.sh 
Hello, Application Version: v2 ; Color: blue OK

Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: blue OK

Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: green OK
```

### 9. Up to how many Pods can be down for upgrade at a time? 

Considering the current strategy settings and number of Pods set to `4` the answer is: **1**

*Attention:* Check the `RollingUpdateStrategy` field when issuing the command:

```bash
$ kubectl describe deploy frontend | grep -i rolling
RollingUpdateStrategy: 25% max unavailable, 25% max surge
```

So, 25% of total number of Pods = 4 => *1 Pod can be down for upgrade at a time!*

### 10. Change the Deployment strategy to `Recreate`

Delete and re-create the Deployment if necessary. Only update the strategy type for the existing Deployment.

```bash
# 1st Way
$ kubectl edit deployment frontend
```

and modify the required field. Make sure to delete the properties of `rollingUpdate` as well, set at `strategy.rollingUpdate`.

```bash
# 2nd Way - Create a new Deployment YAML definition file and apply it after first deleting the old Deployment
$ kubectl apply -f frontend-deploy-recreate-definition.yaml
```

### 8. Upgrade the application by setting the image on the deployment to `kodekloud/webapp-color:v3`

Do **NOT** delete and re-create the deployment. Only set the new image name for the existing deployment.

```bash
$ kubectl edit deployment frontend
```

and modify the image to `kodekloud/webapp-color:v2`. Next, save and exit. The Pods should be recreated with the new image.

Let's check now both the Deployment and the Pods:

```bash
$ kubectl get pods,deploy -o wide
```

### 9. Run the script `curl-test.sh again` 

Notice the failures. Wait for the new application to be ready. Notice that the requests now do not hit both the versions

```bash
./curl-test.sh 
Failed

Failed

Hello, Application Version: v3 ; Color: red OK

Failed

Hello, Application Version: v3 ; Color: red OK

Hello, Application Version: v3 ; Color: red OK

Hello, Application Version: v3 ; Color: red OK

Hello, Application Version: v3 ; Color: red OK
```
