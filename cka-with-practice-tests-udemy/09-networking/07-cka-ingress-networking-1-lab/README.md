***Important:*** We have deployed Ingress Controller, resources and applications. Explore the setup. Note that hey are in different namespaces.

```bash
$ kubectl get all -A -o wide
```

### 1. Which namespace is the Ingress Controller deployed in?

*Answer:* The Ingress Controller is deployed in `ingress-nginx` namespace:

```bash
$ kubectl get all -A -o wide
```

### 2. What is the name of the Ingress Controller Deployment?

*Answer:* The name of the Ingress Controller Deployment is `ingress-nginx-controller`:

```bash
$ kubectl get deploy -A -n ingress-nginx
```

### 3. Which namespace are the applications deployed in?

*Answer:* The applications are deployed in `app-space` namespace:

```bash
$ kubectl get pods -A
```

### 4. How many applications are deployed in the `app-space` namespace?

*Hint:* Count the number of Deployments in this namespace.

*Answer:* The number of Deployments in the `app-space` namespace is **3**.

```bash
$ kubectl get deploy -n app-space -o wide
```

### 5. Which namespace is the Ingress Resource deployed in?

*Answer:* We are talking about the `ingress-wear-watch` Ingress Resource and it is deployed in `app-space` namespace:

```bash
$ kubectl get ingress -A
```

### 6. What is the name of the Ingress Resource?

*Answer:* The name of the Ingress Resource is `ingress-wear-watch`.

```bash
$ kubectl get ingress -n app-space
```

### 7. What is the Host configured on the Ingress Resource?

*Hint:* The host entry defines the domain name that users use to reach the application like `www.google.com`.

*Answer:* The answer is **ALL hosts (*)**. We can check this via two different ways:

```bash
# 1st WAY - Check the HOSTS column when getting the output of the Ingress Resources
$ kubectl get ingress -n app-space

# 2nd WAY - Check the Host under Rules section when issuing below command
$ kubectl describe ingress -n app-space
```

### 8. What backend is the `/wear` path on the Ingress configured with?

*Answer:* We run below command and check the `Backends` column. The answer is **`wear-service`**.

```bash
$ kubectl describe ingress -n app-space
```

### 9. At what path is the video streaming application made available on the Ingress?

*Answer:* The video streaming application is available at `/watch` path on the Ingress.

```bash
$ kubectl describe ingress -n app-space
```

### 10. If the requirement does **NOT** match any of the configured paths what service are the requests forwarded to?

*Answer:* Again we run below command and check this time the `Default` backend field. The answer is `No Service`:

```bash
$ kubectl describe ingress -n app-space
```

### 11. Now view the Ingress Service using the tab at the top of the terminal. Which page do you see?

*Hint:* Click on the tab named Ingress. 

*Answer:* I see the `404 Error page`.

### 12. View the applications by appending `/wear` and `/watch` to the URL you opened in the previous step

*Answer:*

- *URL-1:* https://30080-port-4cf11ea4a7084f36.labs.kodekloud.com/wear
- *URL-2:* https://30080-port-4cf11ea4a7084f36.labs.kodekloud.com/watch

### 13. You are requested to change the URLs at which the applications are made available. Make the video application available at `/stream`

*Hint:*

*Answer:* We need to edit the Ingress Resource pointing to video streaming application from path `/watch` to `/stream`: 

```bash
$ kubectl edit ingress -n app-space 
```

### 14. 

*Hint:*

*Answer:*

```bash

```

### 15. 

*Hint:*

*Answer:*

```bash

```

### 16. 

*Hint:*

*Answer:*

```bash

```

### 17. 

*Hint:*

*Answer:*

```bash

```

### 18. 

*Hint:*

*Answer:*

```bash

```

### 19. 

*Hint:*

*Answer:*

```bash

```

### 20. 

*Hint:*

*Answer:*

```bash

```

### 21. 

*Hint:*

*Answer:*

```bash

```

### 22. 

*Hint:*

*Answer:*

```bash

```

### 23. 

*Hint:*

*Answer:*

```bash

```
