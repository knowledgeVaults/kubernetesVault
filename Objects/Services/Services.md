
A `Service` is a resource that provides a way to access a group of Pods

* A virtual component that lives in the Kubernetes memory (*It doesn't run in a pod therefore it can't be part of the pod network*)
* Provides pods a permanent network endpoint so other applications can always reach them
* A cluster wide virtual object (No interfaces, etc.)
* A service is assigned an IP address from a pre-defined range and the kube-proxy component gets that IP address and create forwarding rules on each node (The Service's IP address is accessible from any node in the cluster)


Create a service and expose a pod on a specific port
```Bash
kubectl expose pod POD_NAME --port PORT_NUMBER --name SERVICE_NAME 
```


```YAML
apiVersion: v1
kind: Service
metadata:
  name: 
spec:
  type: {NodePort|ClusterIP|LoadBalancer}
  ports:
    - name:
      targetPort:
      port:
```