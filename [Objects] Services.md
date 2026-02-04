# Kubernetes: Services

<br>

# Service Options

## ClusterIP

ClusterIP is the default Kubernetes Service type that exposes an application internally within the cluster using a stable virtual private IP address

* The virtual IP is assigned from a reserved range
* Reachable only from inside the cluster 
* The virtual IP remains constant despite Pod restarts or rescheduling

<br>

## NodePort

NodePort exposes an application on a static and high-numbered port (between 30000 and 32767) across each cluster node's IP address

