
**JSONPath** is a query language used to extract specific data from a JSON document

* Allows you to navigate through JSON structures to pick out the exact data you need

## JSONPath in `kubectl`

1. Form and identify the JSONPath query
```Bash
kubectl COMMAND -o json
```

2. Use the JSONPath query with `kubectl`
```Bash
kubectl COMMAND -o=jsonpath='{JSONPATH_QUERY}'
```

[[JSONPath Queries]]

Custom columns
```Bash
kubectl get nodes -o=custom-columns=NODE:.JSONPARTH_QUERY,CPU:.JSONPATH_QUERY
```