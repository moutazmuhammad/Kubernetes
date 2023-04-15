# Network Policy

### * the incoming traffic from the users is an `ingress` traffic.
### * the outgoing request to the app server is `egress` traffic.

### * Kubernetes is configured by default with an all allow rule that allows traffic from any pod to any other pod or services within the cluster.


## What if we do not want the front end web server to be able to communicate with the database server directly?

### - network policy is another object in the Kubernetes namespace just like pods, replica sets or services, you link a network policy to one or more pods. You can define rules within the network policy.
### - In this case, I would say only allow ingress traffic from the API pod on port 3306. Once this policy is created, it blocks all other traffic to the pod and only allows traffic that matches the specified rule.

## How do you apply or link a network policy to a pod?
### - labels and selectors.
### - We label the pod and use the same labels on the port selector field in the network policy and then we build our rule.

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-network-policy
spec:
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: other-app
    ports:
    - protocol: TCP
      port: 80
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: db
    ports:
    - protocol: TCP
      port: 5432

```

##  what if there are multiple API pods in the cluster with the same labels but in different namespaces?

### - that's what the `namespaceSelector` does.
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-network-policy
spec:
  # Only select pods with the "app: my-app" label
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
  - Ingress
  ingress:
  - from:
    ------------------------------------
    - podSelector:
        matchLabels:
          app: other-app
    # Allow traffic from all pods in namespaces with the "env: prod" label
      namespaceSelector:
        matchLabels:
          env: prod
    ------------------------------------
    ports:
    - protocol: TCP
      port: 80

```
> Note the laset expample is deffirent from the next one (-) make 2 selectors

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-network-policy
spec:
  # Only select pods with the "app: my-app" label
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
  - Ingress
  ingress:
  - from:
    ------------------------------------
    - podSelector:
        matchLabels:
          app: other-app
    ------------------------------------
    # Allow traffic from all pods in namespaces with the "env: prod" label
    - namespaceSelector:
        matchLabels:
          env: prod
    ------------------------------------
    ports:
    - protocol: TCP
      port: 80

```

## We could configure a network policy to allow traffic originating from certain IP addresses.
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-network-policy
spec:
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
  - Ingress
  ingress:
  - from:
    - ipBlock:
        # Allow traffic from a specific IP address (10.0.0.1/32)
        cidr: 10.0.0.1/32
    ports:
    - protocol: TCP
      port: 80

```