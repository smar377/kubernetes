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

Test it by browsing to *URL*:

- https://30080-port-4cf11ea4a7084f36.labs.kodekloud.com/stream

### 14. A user is trying to view the `/eat` URL on the Ingress Service. Which page would he see?

*Hint:* If not open already, click on the Ingress tab above your terminal, and append `/eat` to the URL in the browser.

*Answer:* The user should see the `404 Error page` as the `/eat` path is not configured in the Ingress Resource named `ingress-wear-watch`.

### 15. Due to increased demand, your business decides to take on a new venture. You acquired a food delivery company. Their applications have been migrated over to your cluster. Inspect the new Deployments in the `app-space` namespace

```bash
$ kubectl get all -n app-space
```

### 16. You are requested to add a new path to your ingress to make the food delivery application available to your customers. Make the new application available at `/eat`

Specifications:

- Ingress: `ingress-wear-watch
- Path: `/eat`
- Backend Service: `food-service`
- Backend Service Port: 8080`

*Answer:* We need to edit the Ingress Resource named `ingress-wear-watch` and add below new path details to facilitate the food service:

```bash
- backend:
            service:
              name: food-service
              port:
                number: 8080
          path: /eat
          pathType: Prefix
```

```bash
$ kubectl edit ingress -n app-space
```

Last, test the new path by browsing to the following URL:

- https://30080-port-4cf11ea4a7084f36.labs.kodekloud.com/eat

### 17. A new payment service has been introduced. Since it is critical, the new application is deployed in its own namespace. Identify the namespace in which the new application is deployed

*Answer:* After running below command:

```bash
$ kubectl get deploy -A
```

we can see that the new critical payment application Deployment named `webapp-pay` has been deployed in the `critical-space` namespace.

### 18. What is the name of the Deployment of the new application?

*Answer:* The name of the Deployment of the new payment application is `webapp-pay`.

```bash
$ kubectl get deploy -A
```

### 19. You are requested to make the new application available at `/pay`. Identify and implement the best approach to making this application available on the ingress controller and test to make sure its working. Look into `annotations: rewrite-target` as well

*Answer:* We will have to create a new Ingress for the new pay application in the `critical-space` namespace.

**1st WAY** - Build a manifest YAML file (*declarative way*)

We will use below command to know the service and port details: 

```bash
$ kubectl get svc -n critical-space
```

Then we build the YAML manifest file to create the new ingress service to make the application available at `/pay` as follows:

```yaml
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-pay
  namespace: critical-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /pay
        pathType: Prefix
        backend:
          service:
           name: pay-service
           port:
            number: 8282
```

```bash
# Create the Ingress Resource
$ kubectl create -f ingress-pay.yaml

# Check if deployment was successfull
$ kubectl get ingress -n critical-space
$ kubectl describe ingress -n critical-space
```

**2nd WAY** - Create the new Ingress Resource directly from command line (*imperative way*)

```bash
# Create Ingress Resource
$ kubectl create ingress ingress-pay -n critical-space --rule="/pay=pay-service:8282"

# Check if creation was successfull
$ kubectl get ingress -n critical-space
$ kubectl describe ingress -n critical-space
```

Trying to browse in this case to `/pay` URL path, it seems that it does **NOT** work.
We inspect the logs by running:

```bash
$ kubectl get pods -n critical-space
$ kubectl logs <WEBAPP_POD_ID> -n critical-space
```

For this to be fixed we need to add the following annotation:

```yaml
annotations:
  nginx.ingress.kubernetes.io/rewrite-target: /
```

### 20. View the Payment application using the `/pay` URL in your browser. Click on the Ingress tab above your terminal, if its not open already, and append `/pay` to the URL in the browser

*Answer:* Test the newly create Ingress Resource for the payment critical path by browsing to *URL:*

- https://30080-port-4cf11ea4a7084f36.labs.kodekloud.com/pay 
