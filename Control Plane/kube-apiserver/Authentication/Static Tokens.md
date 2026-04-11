**Static Token Authentication** is a simple but insecure authentication mechanism where the [[kube-apiserver]] reads a CSV file of pre-defined bearer tokens at startup

* Each token maps directly to a username, user ID, and optional group
* Tokens are long-lived and do not expire
* Not recommended for any environment beyond local development or testing
* Changes to the token file require an API server restart to take effect

## Token File Format

The token file is a CSV with four columns (group is optional)

```
TOKEN,USERNAME,USER_ID[,GROUP_NAME]
```

Example:
```
abc123secret,alice,u001,developers
xyz789token,bob,u002
system-token,system:serviceaccount:default:mysa,u003
```

## Enabling Static Token Authentication

Pass the file path to the kube-apiserver via the `--token-auth-file` flag

```Bash
# In kube-apiserver.service
--token-auth-file=/etc/kubernetes/static-tokens.csv

# In kubeadm static pod manifest (/etc/kubernetes/manifests/kube-apiserver.yaml)
- --token-auth-file=/etc/kubernetes/static-tokens.csv
```

If using [[kubeadm]], also ensure the file is volume-mounted into the pod:
```YAML
volumeMounts:
  - mountPath: /etc/kubernetes/static-tokens.csv
    name: static-tokens
    readOnly: true
volumes:
  - hostPath:
      path: /etc/kubernetes/static-tokens.csv
    name: static-tokens
```

## Using a Static Token

```Bash
curl -k https://APISERVER:6443/api/v1/pods \
  -H "Authorization: Bearer abc123secret"
```

```Bash
kubectl get pods --token=abc123secret
```

## Why It Is Deprecated

* Tokens never expire (A) leaked token provides permanent access)
* No rotation mechanism (Replacing a token requires editing the file and restarting the API server)
* No audit trail per token use beyond API server access logs
* Superseded by: OIDC tokens (for humans) and projected service account tokens (for pods)

## Security Hardening Alternative

Replace static tokens with short-lived mechanisms:

```Bash
# Create a ServiceAccount token valid for 1 hour (Kubernetes 1.20+)
kubectl create token MY_SERVICE_ACCOUNT --duration=3600s
```

This produces a time-limited JWT that expires automatically, unlike static CSV tokens.
