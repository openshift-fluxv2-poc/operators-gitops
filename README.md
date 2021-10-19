# Managing OpenShift Operators - the GitOps way

This repo demonstates how to manage OpenShift Operators the GitOps way using Flux.

# Problem

You were a platform engineer. You had a long list of Operators. You installed them by clicking. Your OpenShift cluster worked happily.
You broke something. The cluster was falling apart. You forget that long list of Operators you installed.
You can use GitOps to backup and document them. Here's how.



Here's the bootstrapping YAML snippets,
```yaml
GitRepository

Kustomization
```
