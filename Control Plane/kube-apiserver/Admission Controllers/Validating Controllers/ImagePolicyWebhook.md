
**ImagePolicyWebhook** is a built-in admission controller in Kubernetes that lets users enforce custom security policies on container images using a **webhook** (external service)

* Sends a request to an external webhook to check if container images are allowed
* Either allows or rejects the request based on the external webhook's response

| ImagePolicyWebhook Field | Description                                                       | Possible Values                                                                                                                                                 |
| ------------------------ | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `kubeConfigFile`         |                                                                   |                                                                                                                                                                 |
| `defaultAllow`           | Determines the behavior when the webhook backend can't be reached | • `true`: Operates in fail-open mode (All pod creation requests are allowed) <br>• `false`: Operates in fail-closed mode (All pod creation requests are denied) |
| `allowTTL`               |                                                                   |                                                                                                                                                                 |
| `denyTTL`                |                                                                   |                                                                                                                                                                 |
| `retryBackoff`           |                                                                   |                                                                                                                                                                 |