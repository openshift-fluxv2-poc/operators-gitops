# Managing OpenShift Operators - the GitOps way

This repo demonstates how to manage OpenShift Operators the GitOps way using Flux.

# Problem

You were a platform engineer. You had a long list of Operators. You installed them by clicking. Your OpenShift cluster worked happily.
You broke something. The cluster was falling apart. You forget that long list of Operators you installed.
You can use GitOps to backup and document them. Here's how.

Here's the bootstrapping YAML snippets,
```yaml
---
apiVersion: source.toolkit.fluxcd.io/v1beta1
kind: GitRepository
metadata:
  name: 000-root
  namespace: flux-system
spec:
  timeout: 20s
  gitImplementation: libgit2
  interval: 5m
  # If you fork, please change this repo URL to match the forked one.
  url: 'https://github.com/openshift-fluxv2-poc/operators-gitops'
  ref:
    branch: main
---
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: 000-root
  namespace: flux-system
spec:
  timeout: 2m
  path: ./c010-openshift-cluster
  interval: 5m
  prune: true
  force: false
  sourceRef:
    name: 000-root
    kind: GitRepository
```
