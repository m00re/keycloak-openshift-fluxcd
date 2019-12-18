# keycloak-openshift-fluxcd
An Openshift-based recipe for Keycloak in cluster-mode, including FluxCD based Gitops

## Installing Minishift

Simply follow the steps at https://docs.okd.io/latest/minishift/getting-started/installing.html. This
will setup a n Openshift instance and provide access to it with a developer account. This developer
account has access to the Openshift namespace (or project) called `myproject`.

## Preparing the Keycloak installation

TBD

## Setting up FluxCD based GitOps operator

```
fluxctl install \
  --git-user=<yourusername> \
  --git-email=<yourusername>@users.noreply.github.com \
  --git-url=git@github.com:<yourusername>/keycloak-openshift-fluxcd \
  --git-path=keycloak \
  --namespace=myproject | kubectl apply -f -
kubectl -n myproject rollout status deployment/keycloak
fluxctl identity --k8s-fwd-ns myproject
```