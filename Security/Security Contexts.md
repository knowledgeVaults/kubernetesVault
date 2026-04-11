
**Security Contexts** define privilege and access control settings for Pods and individual containers, controlling what the container process is allowed to do on the host

* Settings at the Pod level apply to all containers; container-level settings override Pod-level settings
* Security contexts enforce the principle of least privilege where we deny all unnecessary capabilities by default

## Pod-Level [[Security Contexts]]

```YAML
spec:
  securityContext:
    runAsUser: 1000             # UID for all container processes
    runAsGroup: 3000            # GID for all container processes
    fsGroup: 2000               # GID for volume ownership
    runAsNonRoot: true          # Reject containers that try to run as root
    seccompProfile:
      type: RuntimeDefault      # Apply default seccomp profile
```

## Container-Level Security Context

Overrides Pod-level settings for a specific container

```YAML
spec:
  containers:
    - name: app
      securityContext:
        runAsUser: 1001
        runAsNonRoot: true
        readOnlyRootFilesystem: true   # Immutable container filesystem
        allowPrivilegeEscalation: false
        capabilities:
          drop: ["ALL"]                # Drop all Linux capabilities
          add: ["NET_BIND_SERVICE"]    # Add only what's needed (bind port <1024)
```

## Linux Capabilities

Instead of running as root, containers can be granted specific capabilities

| Capability | Allows |
|---|---|
| `NET_BIND_SERVICE` | Bind to ports below 1024 |
| `SYS_PTRACE` | Trace processes (debugging) |
| `CAP_NET_ADMIN` | Configure network interfaces |

Best practice: `drop: ["ALL"]`, then add back only required capabilities.

## Privileged Containers

`privileged: true` gives the container near-root access to the host (Avoid in production)

```YAML
securityContext:
  privileged: true   # Grants full host access — use only for system-level workloads
```

## Verifying Security Context

```Bash
kubectl exec POD_NAME -- id          # Check running UID/GID
kubectl exec POD_NAME -- whoami      # Check username
kubectl exec POD_NAME -- cat /proc/1/status | grep Cap   # Check capabilities
```
