The **Webhook Configuration Object** is the Kubernetes resource that registers an external admission webhook with the [[kube-apiserver]]

* Two types: `MutatingWebhookConfiguration` and `ValidatingWebhookConfiguration`
* Both follow the same structure — the type determines in which admission phase the webhook is called
* Changes to webhook configurations take effect immediately without restarting the API server

## Full Structure

```YAML
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration   # or MutatingWebhookConfiguration
metadata:
  name: my-webhook
webhooks:
  - name: validate.example.com         # Must be fully qualified domain name
    admissionReviewVersions: ["v1"]    # Supported AdmissionReview versions
    clientConfig:
      service:                         # Webhook server running as a Service
        name: my-webhook-service
        namespace: default
        path: /validate
        port: 443
      caBundle: BASE64_ENCODED_CA_CERT # CA that signed the webhook's TLS cert
    rules:                             # Which requests to intercept
      - apiGroups:   [""]
        apiVersions: ["v1"]
        operations:  ["CREATE", "UPDATE"]
        resources:   ["pods"]
        scope:       "Namespaced"      # or Cluster or *
    namespaceSelector:                 # Optional: only call for pods in matching namespaces
      matchLabels:
        environment: production
    objectSelector: {}                 # Optional: only call for matching objects
    sideEffects: None                  # None | NoneOnDryRun | Some | Unknown
    timeoutSeconds: 10                 # Default 10; max 30
    failurePolicy: Fail                # Fail | Ignore
    matchPolicy: Equivalent            # Exact | Equivalent
```

## Key Fields

| Field | Description |
|---|---|
| `clientConfig.url` | Use instead of `service` to call an external URL |
| `sideEffects: None` | Required for dry-run support |
| `matchPolicy: Equivalent` | Call webhook for all API versions that map to the same resource |
| `reinvocationPolicy: IfNeeded` | Re-run mutating webhook if another webhook modified the object |

## Viewing Registered Webhooks

```Bash
kubectl get mutatingwebhookconfigurations
kubectl get validatingwebhookconfigurations
kubectl describe validatingwebhookconfiguration my-webhook
```
