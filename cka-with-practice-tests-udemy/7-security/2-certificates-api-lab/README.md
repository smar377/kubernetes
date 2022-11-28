### 1. A new member `akshay` joined the team

He requires access to our cluster. The Certificate Signing Request is at the `/root` location.

```bash
$ cat /root/akshay.csr 
```

### 2. Create a `CertificateSigningRequest` object with the name `aksha`y with the contents of the `akshay.csr` file

*Hint:* As of kubernetes 1.19, the API to use for CSR is `certificates.k8s.io/v1`.

Please note that an additional field called `signerName` should also be added when creating CSR. For client authentication to the API server we will use the built-in signer `kubernetes.io/kube-apiserver-client`.

*Answer:* Use this command to generate the `base64` encoded format as following:

```bash
$ cat akshay.csr | base64 -w 0
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQXJzbUEvSjczQThQcmJndEpGK0V0MDhoUjhwNlJUSUd5aXZnQW1STnpESXJnClNmSzE0Uk9lVVVjVUVSOEpKcldaZG9BU3dyYWVuT1FzUWpVMFFtUEZWVFBPdmNuL3h1aUlqby9RSDkxZWFwVUEKby9CVkdROWl0Si9nYUlJWHpzNkRzNW5GTGJkL2VLc1BaSENONXhDbVBGMW82aGdTdXJ4c1NUTUlpL2dkWWlJSApTOEVvZTlZYlVHcXRsSkJZcWttTXhIR2l3OEtJZ2Z1aU5LRFIyTUhFL1JkUWh1OVZSUTk2ZmZSN2twUlhKOWJPCnJUSzg5ZDhoWElMSUR5L1JSWS9IcHZuS3J3K3hQa2dFN1B3NldtLzZmV213R1g5UEV0eVF4U2ljc3Z4SkNBNmsKbldVQXgvR3lwRmZOTklCTFZGeEgwekY0VlVRS2F1TnByU1RtMlBSYkt3SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBS0xtV2FtQ3RUL1NVRVgvdUlVNWZvUFlrdFVrOERoQ2pyTzhtVXVFbjhLK3prcTlvYTBRCnEyZTlGbEFUbjNMTXlLQng4cmk5SDlpeElTTzdWNmRGa0IrTFFIQVpqRDZqNXVwNTFSQ2MrTEo5MVZDWnhnV1MKYmhtMTg1MWpSdk1OSUNsdmtBOUFORFhlVHY4Yi9mbU50bnk0ZFpjenFOTDYrc0xueXlIRkxXcit5bEdqcm5EOQpVTk11a0NkUGVLeUQ1ZGd2aElsUGFDNi9GNDY1S3dDVVNKN0RGNGJCU0hmWEE0UGJZR0x6U1E3RU8wR082eFB5CllJTHRnNEpaSmlPcjFWRHREeDdVYmlDWnZIQ09HM1dGcGt5OS9qYXdFdmhLNjlUdWxFYkhUNnZyZGFpbE1QRkEKckxnbXpKRHlnVWExRGVscnRGSEVZZGJsbHhBNWZuV2lJRjQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
```

and create a YAML format CSR named `akshay-csr.yaml` as follows:

```yaml
---
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQXY4azZTTE9HVzcrV3JwUUhITnI2TGFROTJhVmQ1blNLajR6UEhsNUlJYVdlCmJ4RU9JYkNmRkhKKzlIOE1RaS9hbCswcEkwR2xpYnlmTXozL2lGSWF3eGVXNFA3bDJjK1g0L0lqOXZQVC9jU3UKMDAya2ZvV0xUUkpQbWtKaVVuQTRpSGxZNDdmYkpQZDhIRGFuWHM3bnFoenVvTnZLbWhwL2twZUVvaHd5MFRVMAo5bzdvcjJWb1hWZTVyUnNoMms4dzV2TlVPL3BBdEk4VkRydUhCYzRxaHM3MDI1ZTZTUXFDeHUyOHNhTDh1blJQCkR6V2ZsNVpLcTVpdlJNeFQrcUo0UGpBL2pHV2d6QVliL1hDQXRrRVJyNlMwak9XaEw1Q0ErVU1BQmd5a1c5emQKTmlXbnJZUEdqVWh1WjZBeWJ1VzMxMjRqdlFvbndRRUprNEdoayt2SU53SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBQi94dDZ2d2EweWZHZFpKZ1k2ZDRUZEFtN2ZiTHRqUE15OHByTi9WZEdxN25oVDNUUE5zCjEwRFFaVGN6T21hTjVTZmpTaVAvaDRZQzQ0QjhFMll5Szg4Z2lDaUVEWDNlaDFYZnB3bnlJMVBDVE1mYys3cWUKMkJZTGJWSitRY040MDU4YituK24wMy9oVkN4L1VRRFhvc2w4Z2hOaHhGck9zRUtuVExiWHRsK29jQ0RtN3I3UwpUYTFkbWtFWCtWUnFJYXFGSDd1dDJveHgxcHdCdnJEeGUvV2cybXNqdHJZUXJ3eDJmQnErQ2Z1dm1sVS9rME4rCml3MEFjbVJsMy9veTdqR3ptMXdqdTJvNG4zSDNKQ25SbE41SnIyQkZTcFVQU3dCL1lUZ1ZobHVMNmwwRERxS3MKNTdYcEYxcjZWdmJmbTRldkhDNnJCSnNiZmI2ZU1KejZPMUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

Last, create the CSR object by issuing:

```bash
$ kubectl create -f akshay-csr.yaml
```

### 3. What is the condition of the newly created `CertificateSigningRequest` object?

*Answer:* Its condition is `Pending`.

```bash
$ kubectl get csr
```

### 4. Approve the CSR Request

```bash
$ kubectl certificate approve akshay
```

### 5. How many CSR requests are available on the cluster (including approved and pending)?

*Answer:* There are in total **2** CSR requests available on the cluster, both in `Approved` condition. 

```bash
$ kubectl get csr
```

### 6. During a routine check you realized that there is a new CSR request in place. What is the name of this request?

*Answer:* The new CSR request in place is `agent-smith`.

```bash
$ kubectl get csr
```

### 7. Hmmm... You are NOT aware of a request coming in. What groups is this CSR requesting access to?

*Hint:* Check the details about the request. Preferebly in YAML.

*Answer:* The groups this CSR is requesting access to is `system:masters`.

```bash
$ kubectl get csr agent-smith -o yaml
```

### 8. That doesn't look very right. Reject that request!

```bash
$ kubectl certificate deny agent-smith
$ kubectl get csr
```

### 9. Let's get rid of it. Delete the new CSR object

```bash
$ kubectl delete csr agent-smith
$ kubectl get csr
```
