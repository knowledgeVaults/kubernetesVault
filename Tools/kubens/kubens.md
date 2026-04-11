
[[kubens]] is a CLI tool that simplifies switching the default namespace for the current Kubernetes context without typing `kubectl config set-context` commands

* Companion tool to [[kubectx]]; both are part of the same repository maintained by Ahmet Alp Balkan
* Modifies the `namespace` field of the active context in `~/.kube/config`
* Supports interactive fuzzy search when `fzf` is installed

## Installation

```Bash
# Installed alongside kubectx
brew install kubectx        # macOS (installs both kubectx and kubens)

# Manual
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens

# Via krew
kubectl krew install ns
```

## Usage

```Bash
# List all namespaces in the current cluster (current namespace highlighted)
kubens

# Switch to a namespace
kubens production

# Switch back to the previous namespace
kubens -

# Current namespace
kubens -c
```

## Interactive Mode with fzf

Running `kubens` without arguments and with `fzf` installed opens an interactive selection menu which is ideal for clusters with many namespaces.

## How It Works

`kubens` edits the active context in the [[kubeconfig]] file:

```YAML
# Before
contexts:
  - name: my-cluster
    context:
      cluster: my-cluster
      user: admin
      namespace: default

# After: kubens production
contexts:
  - name: my-cluster
    context:
      cluster: my-cluster
      user: admin
      namespace: production
```

This affects all subsequent `kubectl` commands in the terminal session (No namespace flag needed)

## Equivalent kubectl Command

```Bash
# kubens production is equivalent to:
kubectl config set-context --current --namespace=production
```

`kubens` is simply a more ergonomic wrapper.

## Alias Recommendation

```Bash
alias kn='kubens'
alias kx='kubectx'
```

## Listing Namespaces

`kubens` without arguments lists all namespaces in the current cluster, with the active namespace highlighted

```Bash
kubens
# default
# kube-system
# kube-public
# > production    ← current namespace
# staging
```

The `>` or colour highlight (depending on terminal) marks the active namespace.
