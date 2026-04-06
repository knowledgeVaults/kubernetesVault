
* Requires a controller otherwise the CRD will just be sitting there in etcd

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