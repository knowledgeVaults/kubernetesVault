The **Cloud Controller Manager (CCM)** is the Kubernetes component that integrates cluster operations with cloud-provider-specific APIs

* Separates cloud-specific control logic from the core [[kube-controller-manager]]
* Runs as a standalone [[Deployments]] or [[Daemon Sets]] on control plane nodes
* Each cloud provider (AWS, GCP, Azure, etc.) ships its own CCM implementation
* Introduced to decouple the Kubernetes core from vendor-specific code via the **cloud provider interface**

## Responsibilities

The CCM runs several cloud-specific controllers in a single binary

### Node Controller

Monitors nodes and reconciles their state with the cloud provider's VM inventory

* Adds the `node.kubernetes.io/not-ready` [[Taints]] when the cloud instance cannot be found
* Annotates nodes with cloud-specific metadata (instance type, region, availability zone)
* Deletes node objects when the underlying cloud VM is permanently terminated

### Route Controller

Configures routing rules in the cloud network so pods on different nodes can communicate

* Sets up routes in the cloud VPC/VNet routing table mapping pod CIDRs to node IPs
* Required when using a non-overlay CNI like the AWS VPC CNI or GKE native VPC routing

### Service Controller

Manages cloud load balancers for Services of type [[LoadBalancer]]

* Provisions and deprovisions cloud load balancers (AWS ELB, GCP GCLB, Azure LB) when Service objects are created or deleted
* Reconciles load balancer configuration (ports, health checks, annotations) with the current Service spec

## Cloud Provider Interface

Cloud providers implement a Go interface (`cloud.Interface`) with methods for nodes, zones, instances, load balancers, and routing

* The interface allows any cloud or on-premises provider to plug in their own logic
* In-tree cloud providers are being migrated to out-of-tree CCM implementations via the Cloud Provider Extraction effort

## Deployment Example

```YAML
containers:
  - name: cloud-controller-manager
    image: registry.k8s.io/cloud-controller-manager:v1.30.0
    command:
      - /usr/local/bin/cloud-controller-manager
      - --cloud-provider=aws
      - --leader-elect=true
      - --use-service-account-credentials=true
```
