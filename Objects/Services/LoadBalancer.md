A **[[LoadBalancer]]** Service provisions an external cloud load balancer that routes traffic from the internet to the cluster's pods

* Extends [[NodePort]] because it also creates a [[ClusterIP]] and opens a NodePort on every node
* The cloud provider watches for Services of type `LoadBalancer` and provisions an external LB (AWS ELB, GCP GCLB, Azure LB)
* The `status.loadBalancer.Ingress` field is populated with the external IP or hostname once provisioned

## Manifest

```YAML
apiVersion: v1
kind: Service
metadata:
  name: my-lb-service
  namespace: default
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"    # AWS NLB
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30080    # Optional; auto-assigned if omitted
```

## Traffic Flow

```
Internet → External LB (AWS ELB / GCP GCLB)
   → NODE_IP:30080 (NodePort on any healthy node)
      → kube-proxy DNAT → ClusterIP:80
         → pod:8080
```

## Checking the External IP

```Bash
kubectl get svc my-lb-service
# EXTERNAL-IP column shows the LB IP or hostname (may show <pending> briefly)
kubectl describe svc my-lb-service | grep -A5 "LoadBalancer Ingress"
```

## Annotations for Cloud Customisation

Cloud providers expose LB configuration via annotations

| Annotation | Cloud | Effect |
|---|---|---|
| `service.beta.kubernetes.io/aws-load-balancer-type: nlb` | AWS | Use Network Load Balancer |
| `cloud.google.com/load-balancer-type: Internal` | GCP | Internal-only LB |
| `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` | Azure | Internal LB |

## On-Premises (MetalLB)

On bare-metal clusters without a cloud provider, MetalLB implements the LoadBalancer type using BGP or Layer 2 ARP announcements.

## Deleting a LoadBalancer Service

When you delete a `LoadBalancer` Service, Kubernetes triggers the cloud controller to deprovision the external LB automatically

```Bash
kubectl delete svc my-lb-service
```

Verify the external LB was removed in your cloud provider console to avoid orphaned billing.
