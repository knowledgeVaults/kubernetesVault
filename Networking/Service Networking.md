**[[Service Networking]]** describes how Kubernetes Services provide stable virtual IPs (ClusterIPs) that load-balance traffic across a group of pods

* Services decouple clients from the dynamic set of pods backing a service since pod IPs change on restarts, the Service IP does not
* The Service [[ClusterIP]] is a virtual IP that does not correspond to any actual network interface as it only exists in iptables/IPVS rules
* [[kube-proxy]] on each node programs these rules so that traffic destined for a ClusterIP is DNAT'd to a backend pod IP

## Service Types and Their Networking

| Type           | Reachability    | How It Works                                                            |
| -------------- | --------------- | ----------------------------------------------------------------------- |
| `ClusterIP`    | In-cluster only | Virtual IP in iptables/IPVS; only routable within the cluster           |
| `NodePort`     | Node IP + port  | kube-proxy opens a port on every node; traffic is forwarded to backends |
| `LoadBalancer` | External        | Cloud provider creates an external LB; routes to NodePort on nodes      |
| `ExternalName` | DNS alias       | [[CoreDNS]] returns a CNAME for the service; no proxying involved       |

## How kube-proxy Programs ClusterIP Routing

For a Service `my-service` on ClusterIP `10.96.5.10:80` with pods at `10.1.0.5:8080` and `10.1.0.6:8080`:

```
iptables -t nat:
KUBE-SERVICES → KUBE-SVC-XXXX (match dst 10.96.5.10:80)
KUBE-SVC-XXXX → KUBE-SEP-POD1 (50% probability)
KUBE-SVC-XXXX → KUBE-SEP-POD2
KUBE-SEP-POD1 → DNAT to 10.1.0.5:8080
KUBE-SEP-POD2 → DNAT to 10.1.0.6:8080
```

## Service CIDR

Service ClusterIPs are allocated from the `--service-cluster-ip-range` set on the [[kube-apiserver]] (default `10.96.0.0/12`)

```Bash
kubectl cluster-info dump | grep service-cluster-ip-range
```

## Endpoint and [[Endpoint Slices]] Sync

kube-proxy watches `EndpointSlice` objects (populated by the Endpoints controller) to keep its rules current

```Bash
kubectl get endpointslices -n default
kubectl get endpoints SERVICE_NAME -n default
```

## NodePort Range

NodePorts are allocated from `--service-node-port-range` (default `30000–32767`)

```Bash
kubectl get svc SERVICE_NAME -o jsonpath='{.spec.ports[*].nodePort}'
```
