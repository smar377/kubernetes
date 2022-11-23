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
$ kubectl edit deployment frontend
```
and modify the image to `kodekloud/webapp-color:v2`. Next, save and exit. The Pods should be recreated with the new image.

Let's check now both the Deployment and the Pods:

```bash
$ kubectl get pods,deploy -o wide
```

### 7. Run the script `curl-test.sh` again

Notice the requests now hit both the old and newer versions. However none of them fail

```bash
$ ./curl-test.sh 
Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: green OK

Hello, Application Version: v2 ; Color: green OK
```

### 9. 

*Answer:* 

```bash
$ 
```

### 10. Change the Deployment strategy to `Recreate`

Delete and re-create the Deployment if necessary. Only update the strategy type for the existing Deployment.

```bash
$ kubectl edit deployment frontend
```

and modify the required field. Make sure to delete the properties of `rollingUpdate` as well, set at `strategy.rollingUpdate`.

This method will return an error as we are NOT allowed to edit in this way the Deployment YAML file.

In comparison, what we do is to create a new Deployment definition YAML file:

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: webapp
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        name: webapp
    spec:
      containers:
      - image: kodekloud/webapp-color:v2
        name: simple-webapp
        ports:
        - containerPort: 8080
          protocol: TCP
```

and we deploy it like this:

```bash
$ kubectl apply -f frontend-deploy-recreate-definition.yaml
```

### 8. 

*Answer:* 

```bash
$ 
```
