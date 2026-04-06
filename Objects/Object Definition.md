
```YAML
apiVersion:
kind:
metadata:
spec:
```

## API Version

```YAML
apiVersion:
```

## Object Type

```YAML
kind:
```

## Object Data

* Defined in a form of a dictionary

```YAML
metadata:
```

### Name

```YAML
metadata:
  name:
```

### Label

* `labels:` is a dictionary

```YAML
metadata:
  labels:
    KEY: VALUE
```

### Annotations

The `annotations` field is used to attach metadata to an object that'll be used by tools, controllers, or integrations to store extra information

* Not used by Kubernetes to identify or select objects

```YAML
metadata:
  annotations:
```
## Object Specification

* The `spec:` field is a dictionary

```YAML
spec:
```

### Containers

* A list/array

```YAML
spec:
  containers:
    - name: CONTAINER_NAME
      image: IMAGE[:TAG]
```

### Templates

```YAML
spec:
  - template:
      metadata:
        METADATA
      spec:
        SPECIFICATIONS
```

### Selectors

```YAML
spec:
  selector:
    matchLabels:
      KEY: VALUE
```