### 1. A new member `akshay` joined the team

He requires access to our cluster. The Certificate Signing Request is at the `/root` location.

*Hint:*

*Answer:*

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

and finally, create a YAML format CSR named `akshay-csr.yaml` as follows:

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

### 3. 

*Hint:*

*Answer:*

```bash

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
