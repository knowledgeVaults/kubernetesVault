
## Object Creation Request Lifecycle

### 1. Client Sends a Request

A client builds and sends an HTTPS request to the Kubernetes API endpoint

* [[kubectl]] reads the YAML file and converts it to JSON for the HTTPS request
* Attaches the credentials from the [[kubeconfig]] and other additional options added to the `kubctl apply` command to the HTTPS request

### 2. [[kube-apiserver]] Authenticates the Request

The API server authenticates the caller

### 3. API Server Authorizes the Request

The API server authorizes the caller's request via [[RBAC]] and ABAC

* Request stops at this step if denied by returning a 403 HTTP error code

### 4. API Server Runs the Admission Pipeline

The API server then runs the admission pipeline

#### Mutating Admission Phase

The API server runs all configured mutating admission controllers in order

* Built-in mutating controllers and other MutatingAdmissionWebhooks can mutate the incoming object
* Mutating controllers run before schema and validating stages so that the required defaults and sidecars are in place for when validation occurs

#### Schema Validation Phase

The API server will perform schema validation against the OpenAPI schema for the incoming resource after all mutations have been applied

* Verifies that all required fields exist, types are correct, etc.

#### Validating Admission Phase

The API server will then run the validating admission controllers along with any other ValidatingAdmissionWebhook or ValidatingAdmissionPolicy rules if schema validation passes

### 5. Object Storage

The API server will then convert the incoming object to the correct storage version and write it to [[etcd]] as the new desired state

* `etcd` finalized the creation or update of the incoming object
* The API server generates watch events and notifies all components holding open watch streams on that resource 
* The cluster will start reconciling in order to reach the object's new desired state

### 6. API Server Responds to Client

The API server will convert the stored object back into the requested API version and return a 201 HTTP code with the full object

* `kubectl` prints a short success message and optionally displays the final returned object as stored by the API server in `etcd` if using the `-o yaml/json` flag
