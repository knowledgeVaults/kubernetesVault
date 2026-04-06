
`kubectl` is the primary command-line interface tool for interacting with Kubernetes clusters

* Communicates with the API server to perform operations
* Translates user commands into API requests which enables CRUD operations on Kubernetes objects

## Applying Configurations

When applying a YAML file using the `kubectl apply -f FILENAME.yaml` command, it gets stored on the cluster as YAML file as the Live Object Configuration and then converted into JSON for the Last Applied Configuration

* The Last Applied Configuration helps us figure out what field has been removed from the local YAML file in order to modify the Live Object Configuration to have it reflect the desired state
* The Last Applied Configuration is stored on the Kubernetes cluster in `annotations` under `kubectl.kubernetes.io/last-applied.configuration`
* Kubernetes will compare the local file, Live Object Configuration file, and the Last Applied Configuration to determine what changes are to be made