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

*Answer:* `python app.py`

### 7. Inspect the file `Dockerfile2` given at `/root/webapp-color` directory. What command is run at container startup?

*Answer:* `python app.py --color red`

### 8. Inspect the two files under directory `webapp-color-2`. What command is run at container startup?

Assume the image was created from the `Dockerfile` in this directory.

The `ENTRYPOINT` in the `Dockerfile2` is overridden by the command in the Pod definition file, so the command that will be run is just

- `--color green`

### 9. Inspect the two files under directory `webapp-color-3`. What command is run at container startup?

Assume the image was created from the `Dockerfile` in this directory.

The `ENTRYPOINT` and `CMD` in the `Dockerfile2` is overridden by the `command` and `args` in the Pod definition file, so the command that will be run is:

- `python app.py --color pink`

### 10. Create a Pod with the given specifications 

By default it displays a blue background. Set the given command line arguments to change it to green.

First we create the YAML Pod definition file `webapp-green-pod.yaml` and add all specifications as instructed.

Then we save and deploy the Pod by issuing:

```bash
$ kubectl create -f webapp-green-pod.yaml
$ kubectl get pods webapp-green -o wide
$ kubectl describe pod webapp-green | grep -i -A2 args
```
