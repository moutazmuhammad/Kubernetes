# service accounts in Kubernetes

## ** there are two types of accounts in Kubernetes:
    - user account: is used by humans
    - service account: is used by machines, account used by an application to interact with a Kubernetes cluster.

### For example, a monitoring application like Prometheus uses a service account to pull the Kubernetes API for performance metrics.

### * To create a service account, run the command:
```
kubectl create seviceaccount <sa-name>
```

### * To view the service accounts:
```
kubectl get serviceaccount
```

### * When the service account is created, it also creates a `token` automatically.
### The service account token is what must be used by the external application while authenticating to the Kubernetes API.
### The token, however, is stored as a `secret` object.

### * To see token name (secret), you need to describe service account:
```
kubectl describe serviceaccount <sa-name>
```
### * To view the token:
```
kubectl describe secret <token-name>
```

### So when a service account is created, it first creates the service account object, and then generates a token for the service account. It then creates a secret object and stores that token inside the secret object.


### *  example using curl, you could provide the bearer token as an authorization header while making a REST call to the Kubernetes API.
```
curl https://<api-ip>:<port>/api -insecure --header "Authorization: Bearer <token>
```

### `* You can create a service account, assign the right permissions using role-based access control mechanisms export your service account tokens and use it to configure your third-party application to authenticate to the Kubernetes API.`

## what if your third-party application is hosted on the Kubernetes cluster itself?
### * In that case, this whole process of exporting the service account token and configuring the third-party application to use it can be made simple by automatically `mounting` the service token secret as a volume inside the pod hosting the third-party application.
### * That way, the token to access the Kubernetes API is already placed inside the pod and can be easily read by the application.

### `* Note:` If you go back and look at the list of service accounts, you will see that there is a default service account that exists already. For every name space in Kubernetes, a service account named default is automatically created. Whenever a pod is created, the default service account and its token are automatically mounted to that pod as a volume mount.
    - The secret token is mounted at location /var/run/secret/kubernetes.io/serviceaccount inside the pod.
```
kubectl exec -it <pod-name> --ls /var/run/secret/kubernetes.io/serviceaccount
```

## * To use service account inside pod add field `serviceAccountName` under `spec` section 
## * You may choose not to mount a service account automatically by setting the `automountServiceAccountToken` field to `false` under `spec` section

## To create token for service account `make token name == SA name`
```
kubectl create token <sa-name>
```