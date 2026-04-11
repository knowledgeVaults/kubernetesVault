
A [[Custom Resource Definition]] is a Kubernetes API extension mechanism that allows users to define new resource types

* Once a CRD is registered, the [[kube-apiserver]] accepts, validates, and stores instances of that custom resource in `etcd`
* CRD instances follow the same lifecycle as built-in objects: they are versioned, namespaced or cluster-scoped, and accessible via [[kubectl]]
* CRDs are the foundation for Operators and platform engineering patterns in Kubernetes

## How CRD Data Lands in etcd

When a CRD is created, the API server registers the new resource type and begins serving it

* Instances (Custom Resources) are stored under `/registry/GROUP/RESOURCE/NAMESPACE/NAME` in etcd
* The storage version is defined in the CRD's `spec.versions` (the API server converts between served and stored versions automatically)
* Schema validation at admission time uses the OpenAPI v3 schema embedded in the CRD spec

## Example CRD Storage Key

```
/registry/example.com/myresources/default/my-object
```

## Notable CRDs Documented Here

| CRD       | Group                       | Purpose                                             |
| --------- | --------------------------- | --------------------------------------------------- |
| `Gateway` | `gateway.networking.k8s.io` | Next-gen [[Ingress]] model (see [[Gateway API]].md) |

## Best Practices for CRDs in Production

* Always define an OpenAPI validation schema to prevent malformed custom resources from being admitted
* Use a Conversion Webhook when maintaining multiple CRD versions with different schemas
* Set `preserveUnknownFields: false` (the default in v1 CRDs) to reject unknown fields at admission
* Back up etcd regularly since all CRD instance data lives there alongside built-in object state

## Inspecting CRD Storage in etcd

```Bash
etcdctl get /registry/apiextensions.k8s.io/customresourcedefinitions/   --prefix --keys-only
```

This lists all registered CRDs stored in etcd by their full name.

## Cleanup and Versioning

* When a CRD is deleted, all its Custom Resource instances are also garbage-collected from etcd
* Use `spec.versions[].storage: true` on exactly one version to designate the canonical storage version
* The conversion webhook handles reads/writes between multiple served versions transparently
