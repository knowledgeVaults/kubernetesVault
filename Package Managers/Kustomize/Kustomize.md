
* Allows users to re-use Kubernetes configs and only modify what needs to be changed
* Comes built-in with [[kubectl]] (You may still want to install the [[Kustomize]] CLI to get the latest version since kubectl doesn't come with the latest version)
* Easier and less complex than [[Helm]] although Helm has more features (*conditionals*, *loops*, *functions*, *hooks*, *etc.*)

## Kustomize Configs

```
k8s/
|--> base/
    |--> kustomization.yaml
|--> overlays/
    |--> dev/
        |--> kustomization.yaml
        |--> config-map.yaml
    |--> staging/ 
        |--> kustomization.yaml
        |--> config-map.yaml
```

### Base

* Represent default values

kustomization.yaml
```YAML
# kubernetes resources to be managed by Kustomize
resources:
  - SUBDIRECTORY/
  - SUBDIRECTORY/

# Customizations that need to be made
commonLabels:
  KEY: VALUE
```

Build Kustomize
```
# Combine all the manifests and apply the defined transformation
kustomize build k8s/

# or using kubectl
kubectl apply -k k8/
```

Apply Kustomize builds
```Bash
kustomize build k8s | kubectl apply -f .
```

Delete Kustomize builds
```Bash
kubectl delete -k k8s/
```

Apply Kustomize builds in subdirectories

### Overlays

* Environment specific configurations that add or modify the base configs
* Specify the parameters or configs that you want to override from the base layer

### Final Manifests

* Base + Overlays = Final Kubernetes manifests


## Kustomize Transformers

### Common Transformers

* `commonLabel`: Adds a label to all Kubernetes resources 
```YAML
[...]
commonLabels:
  KEY: VALUE
```

* `namePrefix`/`nameSuffix`: Adds a common prefix or suffix to all resource names
```YAML
namePrefix: NAME_PREFIX
nameSuffix: NAME_SUFFIX
```

* `Namespace`: Adds a common namespace to all resources
```YAML
namespace: NAMESPACE
```

* `commonAnnotations`: Adds an annotation to all resources
```YAML
commonAnnotations:
  ANNOTATION: VALUE
```


### Image Transformers

## Kustomize Patches

* This is another method to modifying Kubernetes configs
* Provides a more "surgical" approach to targeting one or more specific sections in a Kubernetes resource
* Three parameters are needed in order to create a patch:
	* Operation type: Add/Remove/Replace
	* Target: What resource should this patch be applied on (*Kind*, *Namespace*, *etc.*)
	* Value: What is the value that will either be replaced or added with (Only needed for Add/Replace operations)

### Types of Patches

#### JSON 6902 Patch

