### 1. Tshoot user access issues (`USER5` in the example)

Let us inspect the logs of the Pod (`webapp-1`):

```bash
$ kubectl logs -f webapp-1
```

After checking the logs we see multiple events related to `USER5` indicating that:

`USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.`

### 2. Tshoot user issues while trying to purchase an item

Let us inspect the logs of the newly created Pod (`webapp-2`):

**Important"** We must specify the container we want to check the logs for as the newly created Pod contains *two* containers (`simple-webapp` and `db` respectively)

```bash
$ kubectl logs webapp-2 simple-webapp 
```

After checking the logs we see multiple events related to `USER30` indicating that:

`USER30 Order failed as the item is OUT OF STOCK.`
