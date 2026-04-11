**Mutating Admission Controllers** are admission plugins that intercept API requests **before** validation and can modify (mutate) the incoming object

* Run as part of the **Mutating Admission Phase** and is the first admission stage in the API request lifecycle
* Can add, remove, or change fields on the incoming object before it is validated and stored in [[etcd]]
* Multiple mutating controllers run in sequence; each receives the output of the previous one
* Implemented as built-in plugins or external webhooks (`MutatingWebhookConfiguration`)

## Purpose

Mutating controllers allow the cluster to automatically inject defaults, sidecar containers, or required fields that the user did not specify

* **DefaultStorageClass**: injects a default `storageClassName` into PVCs that do not specify one
* **DefaultTolerationSeconds**: adds a default [[Tolerations]] duration for `node.kubernetes.io/not-ready` and `node.kubernetes.io/unreachable` taints
* **MutatingAdmissionWebhook**: calls external webhook servers to apply custom mutations
* **NamespaceAutoProvision**: creates a namespace automatically if a resource is submitted to a non-existent one (see `NamespaceAutoProvision.md`)

## Execution Order

Mutating controllers run before validating controllers

* Mutations must happen first so that validation can check the final, mutated state of the object
* If validation ran before mutation, required fields injected by mutating controllers would cause false validation failures

## MutatingWebhookConfiguration

```YAML
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: my-mutating-webhook
webhooks:
  - name: inject-sidecar.example.com
    admissionReviewVersions: ["v1"]
    clientConfig:
      service:
        name: sidecar-injector
        namespace: default
        path: /mutate
    rules:
      - apiGroups: [""]
        apiVersions: ["v1"]
        operations: ["CREATE"]
        resources: ["pods"]
    sideEffects: None
    failurePolicy: Fail
```

## Failure Policy

* `Fail` (default): if the webhook is unreachable or returns an error, the entire request is rejected
* `Ignore`: if the webhook fails, the request proceeds as if the webhook was never called (Note: use carefully as it silently bypasses mutation)
