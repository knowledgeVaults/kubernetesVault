An **Admission Webhook Server** is an external HTTP(S) service that the [[kube-apiserver]] calls during the admission phase to apply custom mutation or validation logic

* Implements the `AdmissionReview` API — receives a JSON request, returns a JSON response
* Must be reachable from the kube-apiserver over HTTPS (TLS is required in production)
* Registered via `MutatingWebhookConfiguration` or `ValidatingWebhookConfiguration` objects

## AdmissionReview Request

The kube-apiserver sends a `POST` request with an `AdmissionReview` body

```JSON
{
  "apiVersion": "admission.k8s.io/v1",
  "kind": "AdmissionReview",
  "request": {
    "uid": "705ab4f5-6393-11e8-b7cc-42010a800002",
    "kind": {"group": "", "version": "v1", "resource": "pods"},
    "operation": "CREATE",
    "object": { "...pod spec..." },
    "oldObject": null
  }
}
```

## AdmissionReview Response

The webhook must return an `AdmissionReview` with a `response` field

```JSON
{
  "apiVersion": "admission.k8s.io/v1",
  "kind": "AdmissionReview",
  "response": {
    "uid": "705ab4f5-6393-11e8-b7cc-42010a800002",
    "allowed": true,
    "patchType": "JSONPatch",
    "patch": "W3sib3AiOiJhZGQiLCJwYXRoIjoiL3NwZWMvY29udGFpbmVycy8wL2VudiIsInZhbHVlIjpbXX1d"
  }
}
```

* `allowed: true/false` — accept or reject the request
* `patch` — base64-encoded JSON Patch for mutations (mutating webhooks only)
* `status.message` — human-readable rejection reason shown to the user

## Failure Policy

The `defaultAllow` field (in `[[ImagePolicyWebhook]]`) and the webhook's `failurePolicy` field control behavior when the server is unreachable

* `failurePolicy: Fail` — reject the request if the webhook cannot be reached (fail-closed / secure default)
* `failurePolicy: Ignore` — allow the request if the webhook cannot be reached (fail-open / use with caution)

## TLS Configuration

```Bash
# Generate webhook server TLS cert signed by cluster CA
kubectl certificate approve CERTIFICATE_SIGNING_REQUEST_NAME
```

The webhook server certificate must be trusted by the kube-apiserver — use the cluster CA or inject the CA bundle into the webhook configuration.
