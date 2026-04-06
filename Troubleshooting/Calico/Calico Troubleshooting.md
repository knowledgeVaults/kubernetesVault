
```Bash
Events: Type Reason Age From Message ---- ------ ---- ---- ------- Warning Unhealthy 2m58s (x101923 over 10d) kubelet (combined from similar events): Readiness probe failed: 2026-04-05 01:30:18.136 [INFO][3393188] confd/health.go 180: Number of node(s) with BGP peering established = 0 calico/node is not ready: BIRD is not ready: BGP not established with IP_REDACTED
```

Why this happens:
* No routes between nodes
* No Pod networking
* No access to CoreDNS
* No internet