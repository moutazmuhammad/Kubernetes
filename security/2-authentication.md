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