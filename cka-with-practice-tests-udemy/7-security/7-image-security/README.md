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
$ kubectl edit deploy web
$ kubectl get deploy -n default -o yaml
```

Verification:

```bash
$ kubectl describe deploy web | grep -i image
$ kubectl get pod,deploy -o wide -n default
```

### 4. Are the new Pods created with the new images successfully running?

*Answer:* No. One out of three Pods has its status set to `ImagePyllBackOff`.

```bash
$ kubectl get pod -n default -o wide
```

### 5. Create a secret object with the credentials required to access the registry

- Name: private-reg-cred
- Username: dock_user
- Password: dock_password
- Server: myprivateregistry.com:5000
- Email: dock_user@myprivateregistry.com

```bash
$ kubectl create secret docker-registry private-reg-cred --docker-username=dock_user --docker-password=dock_password --docker-server=myprivateregistry.com:5000 --docker-email=dock_user@myprivateregistry.com
```

Verification:

```bash
$ kubectl get secret -o wide
$ kubectl describe secrets private-reg-cred
```

### 6. Configure the Deployment to use credentials from the new secret to pull images from the private registry

*Answer:* We need to edit the Deployment `web` and add:

```bash
imagePullSecrets:
      - name: private-reg-cred
```

under `spec:` section.

```bash
$ kubectl edit deploy web
```

### 7. Check the status of Pods. Wait for them to be running. You have now successfully configured a Deployment to pull images from the private registry

```bash
$ kubectl get pod,deploy -o wide -n default
```
