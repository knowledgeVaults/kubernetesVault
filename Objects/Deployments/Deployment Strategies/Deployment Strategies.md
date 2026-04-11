**[[Deployments]] Strategies** define how a `Deployment` transitions from one version of an application to the next while controlling downtime, speed, and rollback behaviour

## Available Strategies

### RollingUpdate (Default)

Gradually replaces old pods with new ones, maintaining availability throughout the rollout

```YAML
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1    # Max pods that can be down during rollout
    maxSurge: 1          # Max extra pods above desired replica count
```

* Zero-downtime deployments
* Old and new versions run simultaneously during the transition
* `maxUnavailable: 0` + `maxSurge: 1` = fully blue/green style (no old pods removed until new ones are ready)
* See: [[Rolling Update]]

### Recreate

Terminates all existing pods before creating new ones — causes a brief downtime window

```YAML
strategy:
  type: Recreate
```

* Suitable when two versions cannot run simultaneously (shared state, breaking schema changes)
* Simpler rollout tracking; no old/new pod overlap
* See: [[Recreate]]

## Comparison

| Feature | RollingUpdate | Recreate |
|---|---|---|
| Downtime | None | Brief |
| Old/new overlap | Yes | No |
| Resource overhead | Slightly higher (surge pods) | None |
| Best for | Stateless services | Stateful with breaking changes |

## Advanced Patterns (via GitOps/Service Mesh)

Beyond the built-in strategies, teams implement:

* **Blue/Green**: run two identical environments; switch traffic at the LB/[[Ingress]] level
* **Canary**: route a small percentage of traffic to the new version; gradually increase
* **A/B Testing**: split traffic by user segment using header-based routing

These require an Ingress controller (Traefik, NGINX) or service mesh (Istio, Linkerd) as they are not built into Kubernetes Deployments natively.

## Choosing a Strategy

Use **RollingUpdate** for all stateless services in production as it provides zero-downtime deployments with no extra infrastructure. Use **Recreate** only when you must guarantee no two versions overlap, such as when running a database migration that changes the schema in a breaking way.
