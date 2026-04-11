
[[kubectx]] is a CLI tool that simplifies switching between Kubernetes contexts (clusters) without typing long `kubectl config use-context` commands

* Part of the `kubectx` + [[kubens]] toolkit maintained by Ahmet Alp Balkan
* Works directly with the [[kubeconfig]] file (`~/.kube/config`)
* Supports fuzzy search via `fzf` when installed

## Installation

```Bash
# macOS
brew install kubectx

# Linux (direct binary)
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens

# Via krew
kubectl krew install ctx
```

## Usage

```Bash
# List all contexts
kubectx

# Switch to a context
kubectx my-production-cluster

# Switch back to the previous context
kubectx -

# Rename a context
kubectx new-name=old-name

# Delete a context
kubectx -d old-context

# Current context
kubectx -c
```

## Interactive Mode with fzf

When `fzf` is installed, running `kubectx` without arguments opens an interactive fuzzy-search menu to select the target context — ideal when managing many clusters.

```Bash
# Install fzf
brew install fzf    # macOS
apt install fzf     # Debian/Ubuntu
```

## Alias Recommendation

```Bash
alias kx='kubectx'
alias kn='kubens'
```

## How It Works

`kubectx` reads and writes `~/.kube/config` (or `$KUBECONFIG`) directly

* It sets `current-context` to the selected context
* No [[kube-apiserver]] calls are made — it is a purely local config file operation
* Compatible with any kubeconfig-based tooling

## Related: kubens

`kubens` is the companion tool for switching default namespaces

## Integration with Shell Prompt

Display the current context and namespace in your shell prompt using `kube-ps1`

```Bash
brew install kube-ps1
source "$(brew --prefix)/opt/kube-ps1/share/kube-ps1.sh"
PS1='$(kube_ps1) $ '
# Prompt: (⎈ my-cluster:production) $
```

This makes it immediately visible which cluster and namespace you are operating in while preventing accidental changes in production.
