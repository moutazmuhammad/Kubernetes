# Security Primitives

### `API server`
 * The kube-apiserver is at the center of all operations within Kubernetes.
 * We interact with it through the kubectl utility or by accessing the API directly.
 * The first line of defense.


### ** (Who can access the API server?) is defined by the `authentication mechanisms`.
### ** There are different ways that you can authenticate to the API server (Who can access the cluster?):
 * File: username and password.
 * File: username and tokens.
 * Certificates.
 * Integration with external authentication providers like LDAP.
 * For machines, we create `service accounts`.


### ** (what can they do?) is defined by `authorization mechanisms`.
 * Authorization is implemented using role-based access controls (RBAC) where users are associated to groups with specific permissions.
 * Attribute-based access control (ABAC).
 * Node Authorization.
 * Webhooks mode.


### ** All communication with the cluster between the various components such as the ETCD cluster, the kube-controller-manager, scheduler, API server, as well as those running on the worker nodes such as the Kubelet and the kube-proxy is secured using `TLS encryption`.


### ** By default, all Pods can access all other Pods within the cluster. Now, you can restrict access between them using `network policies`.

