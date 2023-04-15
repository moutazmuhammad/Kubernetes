# Image Security

### * Since we have not specified the location where these images are to be pulled from, it is assumed to be Docker's default registry, `Docker hub`. The DNS name for which is `docker.io`.

### * There are many other popular registries as well. Google's registry is at `gcr.io`. where a lot of Kubernetes related images are stored. These are all publicly accessible images that anyone can download and access.

### * you may choose to make a repository private so that it can be accessed using a set of credentials.

### * From Docker's perspective, to run a container using a private image, you first log into your private registry using the Docker login command.
```
docker login <private-registry.io>
```

### * In pod definition file, to use an image from our private registry, we replace the image name with the full path to the one in the private registry.

## How does Kubernetes get the credentials to access the private registry?
### * Within Kubernetes, we know that the images are pulled and run by the Docker run time on the worker nodes.
### * we first create a secret object with the credentials in it.
```
kubectl create secret docker-registry <sectet-name> \
    --docker-server=<private-registry.io> \
    --docker-username=<username> \
    --docker-password=<user-password> \
    --docker-email=<user-email>
```
    - Docker registry is a built in secret type that was built for storing Docker credentials.

### * Then  specify the secret inside our pod definition file under the image pull secret section. (under `spec` section)
```
imagePullSecrets:
- name:<sectet-name>
```
