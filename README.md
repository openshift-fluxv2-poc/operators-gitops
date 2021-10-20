# Managing OpenShift Operators - a GitOps way

This repo demonstates how to manage OpenShift Operators the GitOps way using Flux.

## Problem

You were a platform engineer. You used OpenShift managed clusters. You had a long list of OpenShift Operators, and you installed them manually by clicking. Your OpenShift cluster worked happily. Then, you broke something. The cluster was falling apart. You deleted the old cluster, and created a new one, but you forgot that long list of Operators you had installed.

## Solution

You can use GitOps to backup and document your OpenShift Operators. You could also share them to your team. Here's how.

  1. Your OpenShift Operator is an artifact. Create `_artifacts` directory, and create a directory for each of your OpenShift Operator. For example, `010-flux` for the Flux Operator, `020-gatekeeper` for the Gatekeeper Operator.
  2. Put YAML files of your OpenShift Operator under its directory. For example, `010-flux/flux.yaml`, and `020-gatekeeper/gatekeeper.yaml`. Each directory may contain multiple YAML files.
  3. Wrap each set of artifacts as a Flux's Kustomization object. We call it a feature. Put each feature YAML under `_features` directory.
  4. Create a cluster directory, in this example `c010-openshift-cluster`. Then create a `kustomization.yaml`, and put all features you'd like to have for your cluster as resources, like [this](https://github.com/openshift-fluxv2-poc/operators-gitops/blob/main/c010-openshift-cluster/kustomization.yaml).
  5. Put the following [YAML snippets](https://github.com/openshift-fluxv2-poc/operators-gitops/blob/main/README.md?plain=1#L29) into `root-sync.yaml` like [this](https://github.com/openshift-fluxv2-poc/operators-gitops/blob/main/c010-openshift-cluster/root-sync.yaml) to make everything self-contained and automated after bootstrapping. If you change the bootstrap snippets below, please don't forget to update `root-sync.yaml`.
  6. You are now ready.

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

After bootstrapping, the Flux Operator itself will be also managed by GitOps config from this repository. You could check the labels under the Subscription tab of each Operator to see if it's already managed by Flux and GitOps.

![image](https://user-images.githubusercontent.com/10666/138084572-56e3a46c-fc7d-4d78-8361-8ea2411d3275.png)
![image](https://user-images.githubusercontent.com/10666/138084621-ed766b7a-b885-4ced-9095-0a3c06f122e9.png)

