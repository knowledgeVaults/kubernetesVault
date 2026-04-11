
* Requires a controller otherwise the CRD will just be sitting there in [[etcd]]

```YAML
apiVersion: apiextensions.k8s.io/va
kind: CustomResourceDefinition
metadata:
  name: CRD_NAME
spec:
  # Specify if the CRD is namespaced or not
  scope: {Namespaced}
  # Define the API group
  group: API_GROUP
  # Specify the name of the resources
  names:
    kind: CUSTOM_RESOURCE_NAME
    singular:
    plural:
    shortNames:
      - SHORTNAME
  version:
    - name: v1
      served: {true|false}
      storage: {true|false}
      # Define the spec section of the CRD (Uses open schema API v3)
      schema:
        openAPIV3Schema:  
	      type: object
	      properties:
	        spec:
	          type: object
	          properties:
	            from:
	              type: string
	            to:
	              type: string
	            number:
	              type: integer
```

## Custom Controller

* Created using Go

Build the customer controller in your Kubernetes cluster
```Go
go build -o CONTROLLER_NAME .
```
```Bash
./CONTROLLER_NAME -kubeconfig=$HOME/.kube/config
```

* You can then run the customer controller by building a Docker image and running it inside a Pod

## CRD Lifecycle

Once a CRD is registered, instances (Custom Resources) can be created, read, updated, and deleted just like built-in resources

```Bash
# Create the CRD
kubectl apply -f my-crd.yaml

# Create an instance of the custom resource
kubectl apply -f my-cr.yaml

# Use kubectl like any other resource
kubectl get myresources
kubectl describe myresource my-instance
kubectl delete myresource my-instance
```

## Schema Validation

OpenAPI v3 schema validation is defined in the CRD to reject invalid custom resources at admission time

```YAML
schema:
  openAPIV3Schema:
    type: object
    properties:
      spec:
        type: object
        required: ["replicas", "image"]
        properties:
          replicas:
            type: integer
            minimum: 1
          image:
            type: string
```

## Printer Columns

Custom printer columns control what `[[kubectl]] get` displays

```YAML
additionalPrinterColumns:
  - name: Replicas
    type: integer
    jsonPath: .spec.replicas
  - name: Age
    type: date
    jsonPath: .metadata.creationTimestamp
```

## CRD Versions and Conversion

Multiple versions of a CRD can be served simultaneously; a conversion webhook translates between them

```YAML
versions:
  - name: v1
    served: true
    storage: true   # The stored version
