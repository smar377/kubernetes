## A quick note on editing Pods and Deployments

### Edit a Pod

Remember, you **CANNOT** edit specifications of an existing Pod other than the below:

- spec.containers[*].image
- spec.initContainers[*].image
- spec.activeDeadlineSeconds
- spec.tolerations

For example you cannot edit the environment variables, service accounts, resource limits (all of which we will discuss later) of a running Pod.
But if you really want to, you have two options:

1. Run the `kubectl edit pod <POD_NAME>` command. This will open the Pod specification in an editor (vi editor). Then edit the required properties.
When you try to save it, you will be denied. This is because you are attempting to edit a field on the Pod that is **NOT** editable.
A copy of the file with your changes is saved in a temporary location as shown above.
You can then delete the existing Pod by running the command:

```bash
$ kubectl delete pod webapp
```

Then create a new Pod with your changes using the temporary file:

```bash
$ kubectl create -f /tmp/kubectl-edit-ccvrq.yaml
```

2. The second option is to extract the Pod definition in YAML format to a file using the command:

```bash
$ kubectl get pod webapp -o yaml > my-new-pod.yaml
```

Then make the changes to the exported file using an editor (vi editor) and save the changes:

```bash
$ vi my-new-pod.yaml
```

Then delete the existing Pod by issuing:

```bash
$ kubectl delete pod webapp
```

Then create a new Pod with the edited file:

```bash
$ kubectl create -f my-new-pod.yaml
```

### Edit Deployments

With Deployments you can easily edit any field/property of the Pod template. Since the Pod template is a child of the Deployment specification,  with every change the deployment will automatically delete and create a new pod with the new changes.
So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command.

```bash
$ kubectl edit deployment my-deployment 
```
