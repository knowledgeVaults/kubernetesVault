**Webhook Authorization** allows Kubernetes to delegate authorization decisions to an external HTTP service

* Useful for implementing custom authorization logic not expressible in [[RBAC]] or ABAC
* The [[kube-apiserver]] calls the webhook with a `SubjectAccessReview` request and acts on the response
* Can be combined with other authorization modes in a chain (`--authorization-mode=Node,RBAC,Webhook`)

## Enabling Webhook Authorization

```Bash
--authorization-mode=Webhook
--authorization-webhook-config-file=/etc/kubernetes/webhook-authz.yaml
--authorization-webhook-cache-authorized-ttl=5m
--authorization-webhook-cache-unauthorized-ttl=30s
```

## Webhook Configuration File

The configuration file uses the `[[kubeconfig]]` format to point to the webhook server

```YAML
apiVersion: v1
kind: Config
clusters:
  - name: authz-webhook
    cluster:
      server: https://authz.example.com/authorize
      certificate-authority: /etc/kubernetes/pki/authz-ca.crt
users:
  - name: apiserver
    user:
      client-certificate: /etc/kubernetes/pki/authz-client.crt
      client-key: /etc/kubernetes/pki/authz-client.key
contexts:
  - name: webhook
    context:
      cluster: authz-webhook
      user: apiserver
current-context: webhook
```

## SubjectAccessReview Request/Response

The API server sends a `SubjectAccessReview` to the webhook

```JSON
{
  "apiVersion": "authorization.k8s.io/v1",
  "kind": "SubjectAccessReview",
  "spec": {
    "user": "alice",
    "groups": ["system:authenticated"],
    "resourceAttributes": {
      "namespace": "default",
      "verb": "get",
      "resource": "pods"
    }
  }
}
```

The webhook responds with `status.allowed: true` or `false` and an optional `status.reason`.

## Caching

* `--authorization-webhook-cache-authorized-ttl`: how long to cache `allowed` decisions (reduces latency)
* `--authorization-webhook-cache-unauthorized-ttl`: how long to cache `denied` decisions

## Testing the Webhook

You can manually test a `SubjectAccessReview` against the API server to verify webhook behavior

```Bash
kubectl auth can-i get secrets --as=alice -n production
# yes / no — the decision passes through the webhook if configured
```
