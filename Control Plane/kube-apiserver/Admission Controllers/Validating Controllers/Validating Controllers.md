**Validating Admission Controllers** intercept API requests during the **Validating Admission Phase** after all mutations have been applied and either accept or reject the request based on defined rules

* Run after all mutating controllers, so they validate the final, mutated state of the object
* Cannot modify the incoming object (Only accept or reject)
* Multiple validating controllers may run in parallel; any single rejection fails the request

## Built-in Validating Controllers

| Controller               | Function                                                    |
| ------------------------ | ----------------------------------------------------------- |
| `NamespaceLifecycle`     | Rejects resources in non-existent or terminating namespaces |
| `LimitRanger`            | Enforces `[[Limit Ranges]]` constraints                     |
| `[[Resource Quotas]]`    | Enforces namespace-level resource quotas                    |
| `PodSecurity`            | Enforces Pod Security Standards (baseline, restricted)      |
| `[[ImagePolicyWebhook]]` | Delegates image validation to an external service           |

## External Validating Webhooks

Custom validation logic is implemented via `ValidatingWebhookConfiguration` objects

```YAML
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: my-validator
webhooks:
  - name: validate.example.com
    admissionReviewVersions: ["v1"]
    clientConfig:
      service:
        name: my-validator-svc
        namespace: default
        path: /validate
      caBundle: BASE64_CA
    rules:
      - apiGroups: ["apps"]
        apiVersions: ["v1"]
        operations: ["CREATE", "UPDATE"]
        resources: ["deployments"]
    sideEffects: None
    failurePolicy: Fail
```

## ImagePolicyWebhook

[[ImagePolicyWebhook]] validates container images against an external service before pods are admitted.

## ValidatingAdmissionPolicy (Kubernetes 1.30+ GA)

CEL-based policies without an external webhook server

```YAML
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingAdmissionPolicy
metadata:
  name: require-labels
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ["apps"]
        apiVersions: ["v1"]
        operations: ["CREATE", "UPDATE"]
        resources: ["deployments"]
  validations:
    - expression: "object.metadata.labels.containsKey('app')"
      message: "Deployments must have an 'app' label"
```
