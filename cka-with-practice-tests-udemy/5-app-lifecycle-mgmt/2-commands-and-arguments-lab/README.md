### 1. How many Pods exist on the system? In the current (default) namespace

```bash
$ kubectl get pods -n default -o wide
```

### 2. What is the command used to run the Pod `ubuntu-sleeper`?

```bash
$ kubectl describe pod ubuntu-sleeper | grep -i -A2 command
```

### 3. Create a Pod with the `ubuntu` image to run a container to `sleep` for `5000` seconds

We will modify the file `ubuntu-sleeper-2.yaml`.

*Note:* Only make the necessary changes. Do not modify the name.

We need to add a list with the command and the argument under container `name:ubuntu` Pod section, under `spec:`
Then we create the Pod by issuing:

```bash
$ kubectl create -f ubuntu-sleeper-2.yaml 
```

and check by running the following commands:

```bash
$ kubectl get pods -n default -o wide
$ kubectl describe pod ubuntu-sleeper-2 | grep -i -A2 command
```

### 4. Create a Pod using the file named `ubuntu-sleeper-3.yaml`

There is something wrong with it. Try to fix it!

*Note:* Only make the necessary changes. Do not modify the name

*Answer:* Modify `1200` under command key to `"1200"'.

```bash
# Run and check the Pod deployment as follows
$ kubectl create -f ubuntu-sleeper-3.yaml
$ kubectl describe pod ubuntu-sleeper-3 | grep -i -A2 command
$ kubectl get pods -n default -o wide
```

### 5. Update Pod `ubuntu-sleeper-3` to sleep for 2000 seconds

*Note:* Only make the necessary changes. Do not modify the name of the pod. Delete and recreate the pod if necessary.

We first edit the file `ubuntu-sleeper-3.yaml` and change the sleep value from `"1200"` to `"2000"`.
Then we save, delete the old Pod and create the new one:

```bash
$ kubectl create -f ubuntu-sleeper-3.yaml
$ kubectl get pods -n default -o wide
$ kubectl describe pod ubuntu-sleeper-3 | grep -i -A2 command
```

### 6. Inspect the file `Dockerfile` given at `/root/webapp-color` directory. What command is run at container startup?

*Answer:* 

```bash
$ kubectl describe deploy frontend | grep -i strategy
```

### 7. 

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

### 8. 

Notice the requests now hit both the old and newer versions. However **NONE** of them fail.

```bash
$ 
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
# 1st Way - By editing the Deployment YAML definition file and modifying the image to `kodekloud/webapp-color:v3`
$ kubectl edit deployment frontend
```

```bash
# 2nd Way - By setting the image from command line
$ kubectl set image deploy frontend simple-webapp=kodekloud/webapp-color:v3
```

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
