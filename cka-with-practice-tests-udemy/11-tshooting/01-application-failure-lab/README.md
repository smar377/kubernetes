### Troubleshooting Test 1

*Description:* A simple 2 tier application is deployed in the `alpha` namespace. It must display a green web page on success. Click on the `App` tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

*Hint:* Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.

*Answer:* First we check the error we get:

***`Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can't connect to MySQL server on 'mysql-service:3306' (-2 Name does not resolve)
From webapp-mysql-ddd686475-7mwcg!`***

Initially we check all Deployment, Service and Pod objects deployed in `alpha` namespace in order to check if naming is correct:

```bash
$ kubectl get svc,deploy,pod -n alpha -o wide
`````

We noticed that Service deployed for MySQL is named `mysql` instead of diagram's `mysql-service`. We need to fix that:

```bash
$ kubectl get svc mysql -n alpha -o yaml > mysql-service-alpha.yaml
$ vi mysql-service-alpha.yaml
$ kubectl delete svc mysql -n alpha
$ kubectl create -f mysql-service.yaml
```

Checking afterwards the `mysql-service` on the browser we see that the issue is now solved.

### Troubleshooting Test 2

*Description:* The same 2 tier application is deployed in the `beta` namespace. It must display a green web page on success. Click on the `App` tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

*Hint:* Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.

*Answer:* Let's first inspect the error we get:

***`Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=root; DB_Password=paswrd; 2003: Can't connect to MySQL server on 'mysql-service:3306' (111 Connection refused)`***

As a first thought, it seems to be a network issue. Let's investigate. First we gather all objects created in `beta` namespace:

```bash
$ kubectl get svc,deploy,pod -n beta -o wide
`````

Then the `mysql-service` Service object:

```bash
$ kubectl describe -n beta svc mysql-service
`````
We see that the `targetPort` used to create this service is incorrect. It is set to `8080` instead of `3306` as per architecture diagram. Let's edit the `mysql-service` as per attached YAML manifest file named `mysql-service-beta.yaml`:

```bash
$ kubectl edit service -n beta mysql-service
OR
$ kubectl get svc mysql-service -n beta -o yaml > mysql-service-beta.yaml
$ vi mysql-service-beta.yaml
$ kubectl delete svc -n beta mysql-service
$ kubectl create -f mysql-service-beta.yaml
```

Checking afterwards the `mysql-service` on the browser we see that the issue is now solved.

### Troubleshooting Test 3

*Description:* The same 2 tier application is deployed in the `gamma` namespace. It must display a green web page on success. Click on the `App` tab at the top of your terminal to view your application. It is currently failed or unresponsive. Troubleshoot and fix the issue.

*Hint:* Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.

*Answer:* Let's first inspect the error we get. We see that the page hangs and does **NOT** load.

First, we gather all objects created in `gamma` namespace and check the naming details and selectors:

```bash
$ kubectl get svc,deploy,pod -n gamma -o wide
`````

After inspecting the `selector` of `mysql-service` Service and comparing it with the `label` of `mysql` Pod we see that they are different. We need to fix this:

```bash
# Check and compare
$ kubectl describe svc -n gamma mysql-service | grep -i selector
$ kubectl describe pod -n gamma mysql | grep -i label

# Fix
$ kubectl edit svc -n gamma mysql-service
OR
$ kubectl get svc -n gamma mysql-service -o yaml > mysql-service-gamma.yaml
$ vi mysql-service-gamma.yaml
$ kubectl delete svc -n gamma mysql-service
$ kubectl create -f mysql-service-gamma.yaml
```

Last, trying to browse again the `App` it gives us now a reply!

### Troubleshooting Test 4

*Description:* The same 2 tier application is deployed in the `delta` namespace. It must display a green web page on success. Click on the `App` tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

*Hint:* Stick to the given architecture. Use the same names and port numbers as given in the below architecture diagram. Feel free to edit, delete or recreate objects as necessary.

*Answer:* The error we see is:

***`Environment Variables: DB_Host=mysql-service; DB_Database=Not Set; DB_User=sql-user; DB_Password=paswrd; 1045 (28000): Access denied for user 'sql-user'@'10.42.0.16' (using password: YES)
From webapp-mysql-67785889c9-84krh!`***

The error points out potentially to some `env` variables that have been added to our Deployment.
Let's first gather all objects created in the `delta` namespace:

```bash
$ kubectl get svc,deploy,pod -n delta -o wide
`````

Let us now see the `env` variables that used by our Deployment:

```bash
$ kubectl describe -n delta deploy webapp-mysql | grep -i -A3 environment
```

We see that `DB_User` one is set to `sql-user` while, according to the architectural diagram should be set to `root`. We need to edit the Deployment `webapp-mysql` and fix that:

```bash
$ kubectl edit deploy -n delta webapp-mysql
```

Next we watch till the new Pod with the corrected `env` variables gets created again:

```bash
$ kubectl get pod -n delta -o wide --watch
```

Finally, we try to browse again the `App` and it gives us now a green page!

### Troubleshooting Test 5

*Description:*

*Hint:*

*Answer:*

```bash

`````
### Troubleshooting Test 6

*Description:*

*Hint:*

*Answer:*

```bash

`````
