# Kubeconfig File

### * We learned how to generate a certificate for a user.
### * A client can use the certificate file and key to query the Kubernetes REST API for a list of pods using cURL.
### * To do this, you can send a CURL request to the address of the kube-apiserver while passing in the bearer files and the CA certificate as options.
```
curl https://my-kube-playground:6443/api/v1/pods \
    --key admin.key \
    --cert admin.crt \ 
    --cacert ca.crt
```
### * In this case, the cluster is called "my kube playground".
### * The API server validates the request to authenticate the user.

## ** How do you do that while using the kubectl command?
    * You can specify the same information using the options server, client key, client certificate, and certificate authority with the kubectl utility.
```
kubectl get pods \
    --server my-kube-playground:6443 \
    --client-key admin.key \
    --client-certificate admin.crt \
    --certificate-authority ca.crt
```

### * Typing those in every time is a tedious task  :(
### * So you move this information to a configuration file called as `kubeconfig`. And then specify this file as the kubeconfig option in your command. `$HOME/.kube/config`
```
kubectl get pods \
    --kubeconfig config
```

### * By default, the kubectl tool looks for a file named config under a directory `.kube` under the user's `home` directory.

### * So if you clear the kubeconfig file there, you don't have to specify the path to the file explicitly in the kubectl command.
```
kubectl get pod
```

# Config File Formate
## The config file has three sections:
* `clusters`:
    - Clusters are the various Kubernetes clusters that you need access to multiple clusters for development environment or testing environment or prod or for different organizations or on different cloud providers.
* `users`:
    - Users are the user accounts with which you have access to these clusters. For example, the admin user, a dev user, a prod user.
    - These users may have different privileges on different clusters.
* `contexts`:
    - Contexts define which user account will be used to access which cluster.

## The server specification in our command goes into the cluster section. The admin user's keys and certificates goes into the user section.
    Cluster section:
        --server my-kube-playground:6443
        --certificate-authority ca.crt
    User section:
        --client-key admin.key
        --client-certificate admin.crt

# Real Example
```
apiVersion: v1
kind: Config
current-context: my-kube-context
clusters:
- name: my-kube-cluster
  cluster:
    certificate-authority-data: <base64-encoded-ca-cert>
    server: https://my-kube-api-server:6443
contexts:
- name: my-kube-context
  context:
    cluster: my-kube-cluster
    user: my-kube-user
    namespace: finance
users:
- name: my-kube-user
  user:
    client-certificate-data: <base64-encoded-client-cert>
    client-key-data: <base64-encoded-client-key>
```

## ** How does kubectl know which context to choose from?

### * You can specify the default context used by adding a field `current-context` to the kubeconfig file.

### * There are command line options available within kubectl to view and modify the kubeconfig files.

### `To view the current file being used:`
```
kubectl config view
```

### `To view the current custom config file being used:`
```
kubectl config view --kubeconfig=<my-custom-config>
```

### `To update your current context:`
```
kubectl config use-context <context-name>
```

## `** Can you configure a context to switch to a particular namespace? `
### * Yes.
### * The context section in the kubeconfig file can take additional field called namespace where you can specify a particular namespace.
### * This way, when you switch to that context, you will automatically be in a specific namespace.


