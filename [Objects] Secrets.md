# Kubernetes: Secrets

Secrets are Kubernetes objects that are used to store and manage sensitive data securely and separately from Pod specs and container images

* Holds data as key-value pairs
* Data stored in this object is intended to remain confidential and kept out of manifests and images to reduce accidental exposure
* Stored in etcd and are base64-encoded by default