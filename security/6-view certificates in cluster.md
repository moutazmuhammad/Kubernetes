# View Certificates in Cluster

## It's depends on how the cluster is created

### 1- Hard way

* If you were to deploy a Kubernetes cluster from scratch you generate all the certificates by yourself.
* deploy all the components as native services.

```
cat /etc/systemd/system/kube-apiserver.service
```

* to see logs if you face issue with certificate:

```
journalctl -u etcd.service -l
```

### 2- Using Kubeadm

*  if you were to rely on an automated provisioning tool like kubeadm, it takes care of automatically generating and configuring the cluster for you.
* deploy all the components as pods.

```
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

* to see logs if you face issue with certificate:

```
kubectl logs etcd-master
```

> You can read certificate by use the following command with path of certificate:

```
openssl x509 -in <path-of-certificte> -text -noout 
```

