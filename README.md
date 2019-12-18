# keycloak-openshift-fluxcd
An Openshift-/Kubernetes-based recipe for Keycloak in cluster-mode, including FluxCD based GitOps pattern.

## Installing Minishift

Simply follow the steps at https://docs.okd.io/latest/minishift/getting-started/installing.html. This
will setup a n Openshift instance and provide access to it with a developer account. This developer
account has access to the Openshift namespace (or project) called `myproject`.

## Preparing the Keycloak installation

TBD

## Setting up FluxCD based GitOps operator

First we need to create a configuration for Flux using the CLI utility from 
https://docs.fluxcd.io/en/latest/tutorials/get-started.html. 

```
$ fluxctl install \
  --git-user=$USER \
  --git-email=$USER@users.noreply.github.com \
  --git-url=git@github.com:$USER/keycloak-openshift-fluxcd.git \
  --git-path=keycloak \
  --namespace=myproject > flux/configuration.yaml
```

Since this configuration assumes that you have RBAC enabled on your cluster, which might not be the case (
e.g. when using Minishift and the default user ```developer```), we have to remove the resource definitions
for ```ClusterRole``` and ```ClusterRoleBinding``` manually within ```flux/configuration.yaml```.

We also need to remove Openshift-incompatible settings for the memcached deployment. The ```runAsUser``` 
and ```runAsGroup``` directives are not supported with such UIDs. Hence we simply set the following 
securityContext for the memcached deployment:

```
securityContext:
  privileged: false
```

Afterwards we can apply the Flux configuration to our Openshift cluster.

```
$ kubectl apply -f flux/
```

To be able to access the Git repository using SSH, we need to extract the public key from the Flux deployment.

```
$ fluxctl identity --k8s-fwd-ns myproject
```

Copy & paste this key into your repository as a deploy key, without granting write access.