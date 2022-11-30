### 1. What secret type must we choose for docker registry?

*Answer:* We should run below command and identify it. The answer is **docker-registry**.

```bash
$ kubectl create secret --help
```

### 2. We have an application running on our cluster. Let us explore it first. What image is the application using?

*Answer:* The image used by the application is `nginx:alpine`.

```bash
$ kubectl get pod,deploy -o wide
$ kubectl describe deploy web | grep -i image
```

### 3. We decided to use a modified version of the application from an internal private registry. Update the image of the deployment to use a new image from `myprivateregistry.com:5000`

*Hint:* The registry is located at `myprivateregistry.com:5000`. Don't worry about the credentials for now. We will configure them in the upcoming steps.

*Answer:* We need to edit the Deployment and modify the image name to `myprivateregistry.com:5000/nginx:alpine`.

```bash
$ 
```

### 4. 

*Hint:*

*Answer:*

```bash

```

### 5. 

*Hint:*

*Answer:*

```bash

```

### 6. 

*Hint:*

*Answer:*

```bash

```

### 7. 

*Hint:*

*Answer:*

```bash

```

### 8. 

*Hint:*

*Answer:*

```bash

```

### 9. 

*Hint:*

*Answer:*

```bash

```

### 10. 

*Hint:*

*Answer:*

```bash

```
