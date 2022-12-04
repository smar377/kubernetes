### 1. Identify the DNS solution implemented in this cluster

*Answer:* The DNS solution used in this cluster is **CoreDNS**.

```bash
$ kubectl get pods,rs,deploy -n kube-system | grep coredns
```

### 2. How many Pods of the DNS server are deployed?

*Answer:* There are **2** CoreDNS Pods deployed in the cluster.

```bash
$ kubectl get pods -n kube-system | grep coredns
```

### 3. What is the name of the service created for accessing CoreDNS?

*Answer:* The name of the service created for accessing CoreDNS is called `kube-dns`.

```bash
$ kubectl get svc -n kube-system
```

### 4. What is the IP of the CoreDNS server that should be configured on Pods to resolve services?

*Answer:* The IP of the CoreDNS server that should be configured on Pods to resolve services is `10.96.0.10`.

```bash
$ kubectl get svc -n kube-system
```

### 5. Where is the configuration file located for configuring the CoreDNS service?

*Hint:* Inspect the `Args` field of the `coredns` deployment and check the file used.

*Answer:* After issuing the following command the config file is located in `/etc/coredns/Corefile`.

```bash
$ kubectl describe deploy coredns -n kube-system | grep -A2 Args | grep -i corefile
```

### 6. How is the Corefile passed in to the CoreDNS Pod?

*Answer:* It is passed through the ConfigMap named `coredns` volume in the deployment:

```bash
$ kubectl get configmaps -n kube-system | grep coredns
```

### 7. What is the name of the ConfigMap object created for Corefile?

*Answer:* The name of the ConfigMap object created for Corefile is `coredns`.

```bash
$ kubectl get configmap -n kube-system
```

### 8. What is the root domain/zone configured for this Kubernetes cluster?

*Answer:* After issuing below command we can see that the root domain/zone configured for this Kubernetes cluster is **`cluster.local`**:

```bash
$ kubectl describe configmap coredns -n kube-system
```

### 9. We have deployed a set of Pods and Services in the `default` and `payroll` namespaces. 

*Answer:* Just inspect them and go to the next question:

```bash
$ kubectl get pods -n default -o wide
$ kubectl get pods -n payroll -o wide
```

### 10. What name can be used to access the `hr` web server from the `test` aplication?

*Hint:* You can execute a `curl` command on the `test` Pod to test. Alternatively, the test Application also has a UI. Access it using the tab at the top of your terminal named `test-app`.

*Answer:* After using below command we can see the available services and based on that we can conclude that in order to access the `hr` web server from the `test` application we need to use **`web-service`**.

```bash
$ kubectl get svc -n default -o wide
```

Testing:

```bash
$ kubectl exec -it test -- curl http://web-service:80
```

### 11. Which of the displayed names **CANNOT** be used to access the HR service from the `test` Pod?

*Answer:* After checking the search domain names in the `/etc/resolv.conf` file of `test` Pod we can conclude that we **CANNOT** use `web-service.default.pod`:

```bash
# DNS resolv.conf file on test Pod in order to check the allowed search domains
kubectl exec -it test -- cat /etc/resolv.conf | grep search

# ALL DNS names that CAN be used
$ kubectl exec -it test -- curl http://web-service:80
$ kubectl exec -it test -- curl http://web-service.default:80
$ kubectl exec -it test -- curl http://web-service.default.svc:80
$ kubectl exec -it test -- curl http://web-service.default.svc.cluster.local:80
```

### 12. Which of the below name can be used to access the `payroll` service from the `test` application?

*Answer:* The name that can be used to access the `payroll` service from the `test` application is **`web-service.payroll`**.

```bash
# Check Services running in payroll namespace
$ kubectl get svc -n payroll -o wide

# Check details of test Pod application in default namespace
$ kubectl get pod -n default -o wide
```

Testing:

```bash
# ALL DNS names that CAN be used
$ kubectl exec -it test -- curl http://web-service.payroll:80
$ kubectl exec -it test -- curl http://web-service.payroll.svc:80
$ kubectl exec -it test -- curl http://web-service.payroll.svc.cluster.local:80
```

### 13. Which of the below name **CANNOT** be used to access the `payroll` service from the `test` application?

*Answer:* The name that **CANNOT** be used to access the `payroll` service from the `test` application is **`web-service.payroll.svc.cluster`**:

```bash
# ALL DNS names that CAN be used
$ kubectl exec -it test -- curl http://web-service.payroll:80
$ kubectl exec -it test -- curl http://web-service.payroll.svc:80
$ kubectl exec -it test -- curl http://web-service.payroll.svc.cluster.local:80
```

### 14. We just deployed a web server named `webapp` that accesses a database `mysql` server. However the web server is failing to connect to the database server. Troubleshoot and fix the issue

*Hint:* They could be in different namespaces. First locate the applications. The web server interface can be seen by clicking the tab Web Server at the top of your terminal.

*Answer:*  First we issue below command to identify where are the relevant objects in question deployed, in which namespaces:

```bash
$ kubectl get pods,svc,deploy -A -o wide
```

We see that `webapp` Deployment (and its **1** Pod) is deployed in `default` namespace and `mysql` DB server Pod is deployed in `payroll` namespace.

Then we check the YAML manifest file used to create the Deployment to check if its pointing to the correct `mysql` DNS name:

```bash
$ kubectl get deploy webapp -n default -o yaml | grep -A3 env
```

From above we see that the `DB_Host` value is set to `mysql` but since the entities are located to different namespaces this will **NOT** work. We will need to set the `DB_Host` environment variable of Deployment `webapp` to use `mysql.payroll`. We will run below command and correct the `DB_Host` value:

```bash
$ kubectl edit deploy webapp -n default
```

We save the file, exit and waiting for the Deployment and Pod to be receated.

Last but not least, we test from Deployment's Pod named `webapp-6fd645f564-8tdmv` to see if `port 3306` on `mysql.payroll` is now **OPEN**:

```bash
$ kubectl exec -it webapp-6fd645f564-8tdmv -- nc -vvv mysql.payroll 3306
```

### 15. From the `hr` Pod `nslookup` the `mysql` service and redirect the output to a file `/root/CKA/nslookup.out`

*Answer:* We need to run below command:

```bash
$ kubectl exec -it hr -- nslookup mysql.payroll > /root/CKA/nslookup.out
$ cat /root/CKA/nslookup.out
```
