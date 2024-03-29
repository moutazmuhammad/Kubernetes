# Generate Certificates

* To generate certificates, there are different tools available such as `Easy-RSA, OpenSSL or CFSSL or many others.

# 1] `Generate CA certificates.` 

## 1- Create private key (`ca.key`)
```
openssl genrsa -out ca.key 2048
```

* Then we use the OpenSSL Request command along with the key we just created to generate a certificate signing request.

## 2- generate a certificate signing request (`ca.csr`)
```
openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
```
* The certificate signing request is like a certificate with all of your details, but with no signature.

* In the certificate signing request we specify the name of the component the certificate is for in the Common Name, or CN field.
In this case, since we are creating a certificate for the Kubernetes CA, we name it `Kubernetes-CA`.

## 3- sign the certificate (`ca.crt`)

```
openssl x509 -req -in ca.crt -signkey ca.key -out ca.crt
```

* Since this is for the CA itself it is self-signed by the CA using its own private key that it generated in the first step.

* Going forward for all other certificates. we will use the CA key pair to sign them.


# 2] `Generate the client certificates.`

#  admin user.

## 1- create a private key for the admin user using the OpenSSL command.
```
openssl genrsa -out admin.key 2048
```

## 2- generate a CSR
```
openssl req -new -key admin.key -subj "/CN=Kube-admin" -out admin.csr
```
* it doesn't really have to be Kube Admin. It could be anything, but remember, this is the name that Kube Control client authenticates with and when you run the Kube Control command.

## 3-  generate a signed certificate
```
openssl x509 -req -in admin.csr ca.crt -CAkey ca.key -out admin.crt
```

* But this time you specify the CA certificate and the CA key.

* You're signing your certificate with the CA key pair. That makes this a valid certificate within your cluster.

* That is the certificate that the admin user will use to authenticate to Kubernetes cluster.

## `How do you differentiate this user from any other users?`
* The user account needs to be identified as an admin user and not just another basic user.

* You do that by adding the group details for the user in the certificate.

* In this case, a group named System Masters exists on Kubernetes with administrative privileges.

* you must mention this information in your certificate signing request.

* You can do this by adding group details with the `/O parameter` while generating a certificate signing request.

```
openssl req -new -key admin.key -subj "/CN=kube-admin/O=system:masters" -out admin.crt
```

> We follow the same process to generate client certificates for all other components that access the Kube API server.

# ` what do you do with these certificates?`

* Take the admin certificate, for instance, to manage the cluster.

* You can use this certificate instead of a username and password in a REST API call you make to the Kube API server.
```
curl https://kube-apiserver:6443/api/v1/pods --key admin.key --cert admin.crt --cacert ca.crt
```

* The other way is to move all of these parameters into a configuration file called `kubeconfig`.
That is what most of the Kubernetes clients use.


# Kubeconfig File

### A kubeconfig file is used by the Kubernetes command-line tool, kubectl, to authenticate with a Kubernetes cluster and to specify the cluster, user, and context to use for each command. The kubeconfig file typically contains the following information:

* Cluster configuration: This includes the cluster's server address and the CA certificate used to authenticate the server.
* User authentication: This includes the user's client certificate and private key, or an authentication token.
* Context: This specifies the combination of cluster and user to use for a specific operation.

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
users:
- name: my-kube-user
  user:
    client-certificate-data: <base64-encoded-client-cert>
    client-key-data: <base64-encoded-client-key>
```

* In this example, the kubeconfig file specifies a cluster named my-kube-cluster with a server address of https://my-kube-api-server:6443 and a CA certificate encoded in base64 format.

* The file also specifies a user named my-kube-user with a client certificate and private key encoded in base64 format.

* Finally, the file specifies a context named my-kube-context that combines the my-kube-cluster and my-kube-user settings.


###  In Kubernetes for these various components to verify each other, they all need a copy of the CA's root certificate. So whenever you configure a server or a client with certificates, you will need to specify the CA root certificate as well.