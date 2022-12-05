### Troubleshooting Test 1

*Description:*: A simple 2 tier application is deployed in the `alpha` namespace. It must display a green web page on success. Click on the `App` tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

*Hint:* Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.

*Answer:* Initally we check all Deployment, Service and Pod objects deployed in `alpha` namespace in order to check if naming is correct:

```bash
$ kubectl get svc,deploy,pod -n alpha -o wide
`````

We noticed that Service deployed for MySQL is named `mysql` instead of diagram's `mysql-service`. We need to fix that:

```bash
$ kubectl get svc mysql -n alpha -o yaml > mysql-service.yaml
$ vi mysql-service.yaml
$ kubectl delete svc mysql -n alpha
$ kubectl create -f mysql-service.yaml
```

Checking afterwards the `mysql-service` on the browser we see that the issue is now solved.

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
