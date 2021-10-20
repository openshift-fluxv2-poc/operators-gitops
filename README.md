# Managing OpenShift Operators - the GitOps way

This repo demonstates how to manage OpenShift Operators the GitOps way using Flux.

## Problem

You were a platform engineer. You used OpenShift managed clusters. You had a long list of OpenShift Operators, and you installed them manually by clicking. Your OpenShift cluster worked happily. Then, you broke something. The cluster was falling apart. You deleted the old cluster, and created a new one, but you forget that long list of Operators you had installed.

## Solution

You can use GitOps to backup and document your OpenShift Operators. You could also share them to your team. Here's how.

  1. Your OpenShift Operator is an artifact. Create `_artifacts` directory, and create a directory for each of your OpenShift Operator. For example, `010-flux` for the Flux Operator, `020-gatekeeper` for the Gatekeeper Operator.
  2. Put YAML files of your OpenShift Operator under its directory. For example, `010-flux/flux.yaml`, and `020-gatekeeper/gatekeeper.yaml`. Each directory may contain multiple YAML files.
  3. Wrap each set of artifacts as a Flux's Kustomization object. We call it a feature. Put each feature YAML under `_features` directory.

## Bootstrap via YAML Import

A nice thing is that you can document a bootstrapping YAML on how to get all of your Operators back online for new clusters inside a Markdown doc like the following. Next time, you or your team members could just:

  1. Create a new cluster
  2. Install Flux Operator on the new cluster
  3. Copy and paste YAML snippets and import them into your cluster
  
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

After bootstrapping, the Flux Operator itself will be also managed by GitOps config from this repository.