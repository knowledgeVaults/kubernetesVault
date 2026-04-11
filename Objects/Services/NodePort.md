A **[[NodePort]]** Service exposes a Service on a static port on every node's IP address, making it accessible from outside the cluster

* [[kube-proxy]] opens the same port on every node (default range `30000–32767`)
* External traffic hitting `NODE_IP:NODE_PORT` is forwarded to the Service's [[ClusterIP]], then load-balanced to a healthy pod
* Also creates a ClusterIP because the service remains accessible cluster-internally as well

## Manifest

```YAML
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
  namespace: default
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - name: http
      protocol: TCP
      port: 80          # ClusterIP port (cluster-internal)
      targetPort: 8080  # Container port
      nodePort: 30080   # Node port (30000–32767); omit to auto-assign
```

## Traffic Flow

```
External client → NODE_IP:30080
   → kube-proxy DNAT → ClusterIP:80
      → pod:8080
```

The traffic may reach a pod on a different node since kube-proxy routes across nodes transparently.

## Auto-Assigned NodePort

If `nodePort` is omitted, Kubernetes assigns a random port from the NodePort range

```Bash
kubectl get svc my-nodeport-service -o jsonpath='{.spec.ports[0].nodePort}'
```

## When to Use NodePort

* Quick external access during development or testing
* Clusters without a cloud provider (bare-metal) where [[LoadBalancer]] type is unavailable
* When used behind an external load balancer that forwards to all node IPs

## Limitations

* Ports are exposed on **every** node including the nodes not running a matching pod
* Manual firewall rules needed to allow the NodePort range
* Not recommended for production (use [[LoadBalancer]] or [[Ingress]] instead)
* Only one service per NodePort value cluster-wide

## Accessing a NodePort Service

```Bash
# Get a node IP
kubectl get nodes -o wide | awk '{print $7}'

# Access externally
curl http://NODE_IP:30080

# Test from inside the cluster
kubectl run test --image=busybox --rm -it --restart=Never -- wget -qO- http://my-nodeport-service:80
```
