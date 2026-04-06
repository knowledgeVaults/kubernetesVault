
The `kube-controller-manager` watches the API server for changes to cluster resources via a **List+Watch** mechanism 

* Makes API calls to drive the actual state of an object towards its new desired state (if any)
* Automatically installs all other default controllers 

## Controller Types

There are different controller types that each watch different instances of its resource kind via the API server List+Watch

