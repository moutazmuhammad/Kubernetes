# Configure a Security Context for a Pod or Container

### * when you run a Docker container, you have the option to define a set of security standards, such as the ID of the user used to run the container, the Linux capabilities that can be added or removed from the container ... These can be configured in Kubernetes as well.

## * Containers are encapsulated in Pods. You may choose to configure the security settings at a container level or at a Pod level.

### - If you configure it at a Pod level, the settings will carry over to all the containers within the Pod.

### - the settings on the container will override the settings on the Pod.

### ** To configure security context on the container, add a field called `securityContext` under the spec section of the Pod.
```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
```

### ** To set the same configuration on the container level, move the whole section under the container specification like this.
```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    securityContext:
        runAsUser: 1000
```

### ** To add capabilities, use the capabilities option and specify a list of capabilities to add to the Pod.
```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    securityContext:
        runAsUser: 1000
        capabilities:
            add: ["NET_ADMIN", "SYS_TIME"]
```

> Note: capabilities are only supported at the container level and not at the pod level.