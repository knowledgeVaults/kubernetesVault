
In Traefik, a **Router** is the component that decides whether and where to forward an incoming request by matching it against a set of rules and directing it to a service

* Routers are defined implicitly via Kubernetes [[Ingress]] annotations or explicitly via `IngressRoute` CRDs
* Each router is associated with one or more EntryPoints (the ports Traefik listens on) and a set of match rules
* Middlewares can be chained to a router to apply transformations before the request reaches the backend

## Router Components

```
EntryPoint → Router (match rules) → Middlewares → Service → Backend pods
```

## Match Rules

Routers use a rule expression language to match requests

```
Host(`example.com`) && PathPrefix(`/api`)
```

| Rule | Example | Matches |
|---|---|---|
| `Host()` | `Host("app.example.com")` | Requests to this hostname |
| `PathPrefix()` | `PathPrefix("/api")` | Paths starting with `/api` |
| `Path()` | `Path("/health")` | Exact path match |
| `Method()` | `Method("GET", "POST")` | Specific HTTP methods |
| `Header()` | `Header("X-Custom", "value")` | Requests with a specific header |
| `Query()` | `Query("debug", "true")` | Requests with query param |
| `ClientIP()` | `ClientIP("192.168.1.0/24")` | Source IP CIDR |

Rules combine with `&&` (AND) and `||` (OR).

## Priority

When multiple routers match the same request, Traefik selects the most specific one based on rule length. Longer, more specific rules have higher priority.

```
Host("app.example.com") && PathPrefix("/api/v2")   → higher priority
Host("app.example.com") && PathPrefix("/api")       → lower priority
```

## Viewing Routers

The Traefik dashboard (`/dashboard/`) shows all active routers, their rules, and which service they point to.

```Bash
kubectl port-forward -n traefik svc/traefik-dashboard 9000:9000
# Open http://localhost:9000/dashboard/
```

## Router Priority Example

```YAML
routes:
  - match: Host(`app.example.com`) && PathPrefix(`/admin`)
    priority: 100    # Explicitly set priority; higher wins on ties
  - match: Host(`app.example.com`) && PathPrefix(`/`)
    priority: 10
```

Explicit priorities are useful when automatic length-based priority produces unexpected results.
