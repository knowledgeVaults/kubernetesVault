
```YAML
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: RESOURCE_NAME
webhooks:
```

A `MutatingWebhookConfiguration` registers external webhooks that are called during the **mutating admission phase**, allowing modification of incoming API objects before they are validated and stored

```YAML
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: sidecar-injector
webhooks:
  - name: inject.sidecar.example.com
    admissionReviewVersions: ["v1"]
    clientConfig:
      service:
        name: sidecar-injector-svc
        namespace: default
        path: /inject
      caBundle: BASE64_CA_CERT
    rules:
      - apiGroups: [""]
        apiVersions: ["v1"]
        operations: ["CREATE"]
        resources: ["pods"]
    sideEffects: None
    failurePolicy: Fail
    namespaceSelector:
      matchLabels:
        injection: enabled
    reinvocationPolicy: IfNeeded
```

## Key Fields

| Field | Description |
|---|---|
| `clientConfig.service` | Points to an in-cluster Service exposing the webhook |
| `clientConfig.url` | Alternative: direct HTTPS URL for out-of-cluster webhooks |
| `sideEffects: None` | Required for dry-run compatibility |
| `failurePolicy: Fail` | Reject request if webhook is unreachable (secure default) |
| `reinvocationPolicy: IfNeeded` | Re-call this webhook if another webhook changed the object |

## Viewing Registered Webhooks

```Bash
kubectl get mutatingwebhookconfigurations
kubectl describe mutatingwebhookconfiguration sidecar-injector
```

## Common Use Cases

* Istio / Linkerd sidecar injection
* Vault Agent Injector (injects init/sidecar for [[Secrets]] retrieval)
* OPA/Gatekeeper policy mutation
* Default resource requests injection


## Temporarily Disabling a Webhook

```Bash
# Set failurePolicy to Ignore to allow requests through if webhook is down
kubectl patch mutatingwebhookconfiguration sidecar-injector \
  --type=json -p='[{"op":"replace","path":"/webhooks/0/failurePolicy","value":"Ignore"}]'
```
