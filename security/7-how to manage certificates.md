# How to manage certificates in kubernetes?

* I am the only administrator and user of the cluster and I have my own admin certificate and key.

* A new admin comes into my team. She needs access to the cluster. We need to get her a pair of certificate and key pair for her to access the cluster.

* She creates her own private key, generates a certificate signing request, and sends it to me.

*  I'm the only admin, I then take the certificate signing request to my CA server, gets it signed by the CA server using the CA server's private key and route certificate, thereby generating a certificate and then sends the certificate back to her.

* She now has her own valid pair of certificate and key that she can use to access the cluster.

* The certificates have a validity period. It ends after a period of time. Every time it expires, we follow the same process of generating a new CSR and getting it signed by the CA.


# what is the `CA server` and where is it located in the Kubernetes setup?

* The CA is really just the pair of key and certificate files we have generated. Whoever gains access to these pair of files can sign any certificate for the Kubernetes environment.

* They can create as many users as they want put whatever privileges they want. So these files need to be protected and stored in a safe environment.

* we place them on a server that is fully secure, now that server becomes your CA server.

* The certificate key file is safely stored in that server and only on that server. Every time you want sign a certificate you can only do it by logging into that server.

* CA pair of files and stores that on the master node itself.

### when the users increase and your team grows you need a better automated way to manage the certificates, signing requests, as well as to rotate certificates when they expire.

### `Kubernetes has a built in certificates API that can do this for you.`

* With the certificates API, you now send a certificate signing request directly to Kubernetes through an API call.

* This time when the administrator receives a certificate signing request, instead of logging onto the master node and signing the certificate by himself, he creates a Kubernetes `API object called certificate signing request`.

* The request can be reviewed and approved easily using Kube control commands.

### This certificate can then be extracted and shared with the user.
- A user first creates a key
- then generates a certificate signing request, using the key with her name on it.
- then sends the request to the administrator.
- The administrator takes a key and creates a certificate signing request object.
- The certificate signing request object is created like any other Kubernetes object using a manifest file with the usual fields.


#  `Full example of a Certificate Signing Request (CSR) object in Kubernetes`

## 1- Create a private key for the certificate:
```
openssl genrsa -out admin.key 2048
```
## 2- Create a CSR file using the private key:
```
openssl req -new -key admin.key -out admin.csr -subj "/CN=admin/O=Example Company"
```
## 3- Base64-encode the CSR:
```
cat admin.csr | base64 | tr -d '\n' > admin.csr.b64
```
## 4- Create a CSR object in Kubernetes using the base64-encoded CSR:
```
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: admin-csr
spec:
  request: <base64-encoded-csr>
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - digital signature
  - key encipherment
  username: admin-username
  uid: "1000"
  groups:
  - developers
  - admins
  extra:
    my-key: my-value

```
    - `request`: The base64-encoded CSR.
    - `signerName`: The name of the Kubernetes component that will sign the CSR.
    - `usages`: The intended usages for the certificate that will be issued based on this CSR.
    - `username`: The name of the user for whom the certificate is being requested.
    - `uid`: The UID of the user for whom the certificate is being requested.
    - `groups`: The groups to which the user for whom the certificate is being requested belongs.
    - `extra`: Additional information to include in the CSR, in the form of key-value pairs.

## 5- Apply the CSR object to the cluster:
```
kubectl apply -f admin-csr.yaml
```
## 6- Approve the CSR as a cluster administrator:
```
kubectl certificate approve admin-csr
```
## 7- Retrieve the signed certificate:
```
kubectl get csr admin-csr -o jsonpath='{.status.certificate}' | base64 --decode > admin.crt
```
## 8- To get crtificates
```
kubectl get crt <crt-name> -o yaml
```
- Now, the `admin.crt` file contains the signed certificate that corresponds to the private key in `admin.key`. This certificate can be used to authenticate as an admin user in Kubernetes. To use the certificate, you can create a Kubernetes `kubeconfig file` that references the certificate and the corresponding private key.

> All the certificate related operations are carried out by the `controller manager`.