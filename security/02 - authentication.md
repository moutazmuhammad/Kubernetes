# Authentication

### ** The different users that may be accessing the cluster.
    * Two types of users: 
        - Humans such as the administrators and developers.
        - Bots such as processes, services, or applications.

### ** You `cannot` create users in a Kubernetes cluster, or view the list of users like pods for example.
``` 
kubectl create user username  X

kubectl get users             X
``` 

### ** However, in case of service accounts, Kubernetes can manage them. You `can create and manage service accounts` using the Kubernetes API.
```
kubectl create serviceaccount sa1

kubectl get serviceaccount
```

### ** Kubernetes does not manage user accounts natively.
### ** All user access is managed by the `API server` whether you're accessing the cluster through Kubectl tool, or the API directly.

### ** The Kube API Server authenticates the request before processing it.

### ** There are different authentication mechanisms that can be configured:
    * username and passwords in a static password file.
    * usernames and tokens in a static token file.
    * authenticate using certificates.
    * another option is to connect to third party authentication protocols, like LDAP, Kerberos, et cetera.

## 1- static password and token files
- You can create a list of users and their passwords in a `CSV file` and use that as the source for user information.

- The file has three columns: password, username, and user ID.
-----------------------
password1,user1,userID1
password2,user2,userID2
password3,user3,userID3
-----------------------

- Then pass the file name as an option to the Kube API Server service `kube-apiserver.service`.
```
--baic-auth-file=user-details.csv
```

- Remember, the Kube API Server `service` and the various options that is where you must specify this option.
- You must then restart the Kube API Server

- If you set up your cluster using the Kubeadm tool, then you must modify the Kube API Server pod definition file. the Kubeadm tool will automatically restart the Kube API Server once you update this file.

- To authenticate using the basic credentials while accessing the API server, specify the user and password in a curl command like this: 
```
 curl -v -k https://master-node-ip:6443/api/vi/pods -u "<username>:<password>"
```

- In the CSV file with the user details that we saw, we can optionally have a fourth column with the group details to assign users to specific groups.
```
                                ------------------------------
                                password1,user1,userID1,group1
                                password2,user2,userID2,group1
                                password3,user3,userID3,group2
                                ------------------------------
```


## 2- Static token file
- Similarly, instead of a static password file, you can have a static token file.
- Instead of password, you specify a token.
```
                                ---------------------------
                                token1,user1,userID1,group1
                                token2,user2,userID2,group1
                                token3,user3,userID3,group2
                                ---------------------------
```

- Then pass the file name as an option to the Kube API Server sevice.
```
--token-auth-file=user-details.csv
```

- Also you can specify the token as an authorization bearer token to your request like this.
```
curl -v -k https://master-node-ip:6443/api/vi/pods `--header "Authorization:Bearer <token>"`
```

## Remember that this authentication mechanism that stores username, passwords and tokens in clear text in a static file `is not a recommended approach` as it is insecure.


# Follow the below instructions to configure basic authentication in a kubeadm setup.

* Create a file with user details locally at `/tmp/users/user-details.csv`
```
                                ------------------------------
                                password1,user1,userID1,group1
                                password2,user2,userID2,group1
                                password3,user3,userID3,group2
                                ------------------------------
```

* Edit the kube-apiserver static pod configured by kubeadm to pass in the user details. The file is located at `/etc/kubernetes/manifests/kube-apiserver.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
      <content-hidden>
    image: k8s.gcr.io/kube-apiserver-amd64:v1.11.3
    name: kube-apiserver
    volumeMounts:
    - mountPath: /tmp/users
      name: usr-details
      readOnly: true
  volumes:
  - hostPath:
      path: /tmp/users
      type: DirectoryOrCreate
    name: usr-details
```

* Modify the kube-apiserver startup options to include the basic-auth file
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --authorization-mode=Node,RBAC
      <content-hidden>
    - --basic-auth-file=/tmp/users/user-details.csv
```

* Create the necessary roles and role bindings for these users:

```
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
 
---
# This role binding allows "jane" to read pods in the "default" namespace.
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: user1 # Name is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```

* Once created, you may authenticate into the kube-api server using the users credentials
```
curl -v -k https://localhost:6443/api/v1/pods -u "user1:password123"
```