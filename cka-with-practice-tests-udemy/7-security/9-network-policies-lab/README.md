### 1. How many network policies do you see in the environment?

*Hint:* We have deployed few web applications, services and network policies. Inspect the environment.

*Answer:* We see only **1** network policy deployed named `payroll-policy`.

```bash
$ kubectl get networkpolicy
OR
$ kubectl get netpol
```

### 2. What is the name of the Network Policy?

*Answer:* It is called `payroll-policy`. Network policy is namespace-scoped so list in the `default` namespace.

```bash
$ kubectl get netpol
```

### 3. Which Pod is the Network Policy applied on?

*Hint:* Use the command `kubectl get netpol` and look under the `Pod Selector` column.

*Answer:* The network policy `payroll-policy` is applied on Pod named `payroll'.

```bash
$ kubectl get netpol
```

### 4. What type of traffic is this Network Policy configured to handle?

*Answer:* The type of traffic this Network Policy is configured to handle is `Ingress`.

```bash
$ kubectl get netpol payroll-policy -o yaml | grep -i policytypes
$ kubectl describe netpol payroll-policy | grep -i 'policy\stypes'
```

### 5-6. What is the impact of the rule configured on this Network Policy?

*Answer:* Traffic **FROM** `internal` to `payroll` Pod is allowed and `internal` Pod can access `port 8080` on `payroll` Pod.

```bash
$ kubectl get netpol payroll-policy -o yaml
$ kubectl describe netpol payroll-policy
```

### 7. Perform a connectivity test using the User Interface in these Applications to access the `payroll-service` at `port 8080`

*Answer:* After checking and running the tests on both UIs in these Applications to try and access the `payroll-service` at `port 8080` we see that *ONLY* internal application can access `payroll-service`. 

### 8. Perform a connectivity test using the User Interface of the Internal Application to access the `external-service` at `port 8080`

*Answer:* After checking and running the test Internal Application UI to try and access the `payroll-service` at `port 8080` we see that it is successful. 

### 9. Create a network policy to allow traffic from the `internal` application only to the `payroll-service` and `db-service`

*Hint:* Use the spec given below. You might want to enable ingress traffic to the Pod to test your rules in the UI.

First we check the resources configured in the Kubernetes cluster:

```bash
$ kubectl get pod,svc -n default -o wide
```

Next we create the YAML manifest file for this network policy named `internal-policy.yaml` as follows:

```yaml
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
  - Egress
  - Ingress
  ingress:
    - {}
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - protocol: TCP
      port: 3306

  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - protocol: TCP
      port: 8080

  - ports:
    - port: 53
      protocol: UDP
    - port: 53
      protocol: TCP
```

**Note:** We have also allowed `Egress` traffic to TCP and UDP port. This has been added to ensure that the internal DNS resolution works from the internal pod. *Remember: The `kube-dns` service is exposed on port 53:*

```bash
$ kubectl get svc -n kube-system 
```

Last but not least we create the network policy by issuing:

```bash
$ kubectl create -f internal-policy.yaml
```

Verification:

```bash
$ kubectl get netpol -n default
$ kubectl describe netpol internal-policy
```
