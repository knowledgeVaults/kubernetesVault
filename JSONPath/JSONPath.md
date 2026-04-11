**[[JSONPath]]** is a query language used to navigate and extract data from JSON structures, supported natively in [[kubectl]] for custom output formatting

* Used with `kubectl` via the `-o jsonpath='{QUERY}'` flag
* Syntax is derived from XPath but applied to JSON
* Enables extracting specific fields, iterating over arrays, and building custom output formats

## JSONPath in kubectl

### Step 1: Inspect the full JSON

```Bash
kubectl get pod MY_POD -o json
kubectl get nodes -o json
```

### Step 2: Build the query

```Bash
# Single field
kubectl get pod MY_POD -o jsonpath='{.metadata.name}'

# Nested field
kubectl get pod MY_POD -o jsonpath='{.status.podIP}'

# Array item (first element)
kubectl get pod MY_POD -o jsonpath='{.spec.containers[0].image}'

# All items in an array
kubectl get pod MY_POD -o jsonpath='{.spec.containers[*].name}'

# Iterate over all items
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.status.capacity.cpu}{"
"}{end}'
```

## Common JSONPath Patterns

| Pattern | Meaning |
|---|---|
| `.fieldName` | Access a field |
| `['field-with-dash']` | Access field with special characters |
| `[N]` | Access array index N (0-based) |
| `[*]` | All elements in an array |
| `range .items[*]` ... `end` | Loop over items |
| `?(@.field=="value")` | Filter expression |

## Custom Columns

Build tabular output with selected fields

```Bash
kubectl get nodes -o custom-columns=NAME:.metadata.name,CPU:.status.capacity.cpu,MEM:.status.capacity.memory

kubectl get pods -o custom-columns=NAME:.metadata.name,NODE:.spec.nodeName,STATUS:.status.phase
```

## Sorting

```Bash
kubectl get pods --sort-by='.metadata.creationTimestamp'
kubectl get pv --sort-by='.spec.capacity.storage'
```

## Filter Expressions

```Bash
# Get pods not in Running state
kubectl get pods -o json | jq '.items[] | select(.status.phase != "Running") | .metadata.name'

# JSONPath filter
kubectl get pods -o jsonpath='{.items[?(@.status.phase=="Pending")].metadata.name}'
```

## Printing Multiple Fields

```Bash
# Tab-separated: name, status, node
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.status.phase}{"	"}{.spec.nodeName}{"
"}{end}'
```
