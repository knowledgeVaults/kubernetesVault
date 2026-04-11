A **Service** is a Kubernetes resource that provides a stable virtual IP and DNS name for accessing a group of Pods, decoupling clients from the dynamic set of Pod IPs

* Pods are ephemeral since their IPs change on restarts; Services provide a permanent endpoint
* Uses `spec.selector` to dynamically track healthy backend Pods
* [[kube-proxy]] programs iptables/IPVS rules on every node so the [[ClusterIP]] is reachable cluster-wide

## Service Types

| Type             | Reachability          | Use Case                                  |
| ---------------- | --------------------- | ----------------------------------------- |
| [[ClusterIP]]    | Cluster-internal only | Internal service-to-service communication |
| [[NodePort]]     | Node IP + fixed port  | External access via node (dev/testing)    |
| [[LoadBalancer]] | Cloud load balancer   | Production external access on cloud       |
| `ExternalName`   | DNS CNAME alias       | Route traffic to external FQDNs           |

## Full Service Manifest

```YAML
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: default
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
    - name: http
      protocol: TCP
      port: 80          # Service port (what clients connect to)
      targetPort: 8080  # Container port (where the app listens)
```

## Port Fields

| Field | Description |
|---|---|
| `port` | Port on the Service ClusterIP that clients connect to |
| `targetPort` | Port on the Pod container where traffic is forwarded |
| `nodePort` | Port exposed on every node's IP (NodePort/LoadBalancer only; 30000–32767) |
| `protocol` | `TCP` (default), `UDP`, `SCTP` |

## Imperative Commands

```Bash
kubectl expose pod POD_NAME --port=80 --target-port=8080 --name=my-svc
kubectl expose deployment DEPLOY_NAME --port=80 --type=ClusterIP
kubectl get endpoints my-service    # See which pod IPs are backing the service
```

## Headless Services

Setting `clusterIP: None` creates a headless service since DNS returns individual pod IPs instead of a single ClusterIP

* Used by StatefulSets for stable per-pod DNS names

## ExternalName Service

Routes traffic to an external DNS name — no proxying, just a CNAME in [[CoreDNS]]

```YAML
spec:
  type: ExternalName
  externalName: my.database.example.com
```
