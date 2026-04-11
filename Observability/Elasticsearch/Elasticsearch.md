
**[[Elasticsearch]]** is a distributed, RESTful search and analytics engine used in Kubernetes observability stacks as the backend storage and search layer for container logs

* Part of the **EFK stack**: Elasticsearch + [[Fluent Bit]] (or Fluentd) + Kibana
* Provides full-text search, structured queries, and aggregations over log data
* Deployed as a [[Stateful Sets]] in the cluster or as a managed external service

## Role in the Kubernetes Logging Pipeline

```
Container stdout/stderr
       │
  Fluent Bit DaemonSet (log shipper)
       │ (HTTP/JSON)
       ▼
  Elasticsearch (storage + indexing)
       │
  Kibana (query + visualisation)
```

## Elasticsearch Concepts

| Concept | Description |
|---|---|
| **Index** | A collection of documents with similar structure (e.g., one per day: `logstash-2024.06.01`) |
| **Document** | A single JSON log entry |
| **Shard** | A horizontal partition of an index for scalability |
| **Replica** | A copy of a shard for fault tolerance |
| **Node** | A running Elasticsearch instance |

## Kubernetes [[Deployments]] (ECK / Elastic Cloud on Kubernetes)

The Elastic Cloud on Kubernetes (ECK) operator is the recommended way to run Elasticsearch in Kubernetes

```Bash
kubectl apply -f https://download.elastic.co/downloads/eck/latest/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/latest/operator.yaml
```

```YAML
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: quickstart
spec:
  version: 8.12.0
  nodeSets:
    - name: default
      count: 3
      config:
        node.store.allow_mmap: false
```

## Querying Logs

```Bash
# Port-forward to Kibana
kubectl port-forward service/quickstart-kb-http 5601 -n elastic

# Or query Elasticsearch directly
curl -u elastic:PASSWORD https://localhost:9200/logstash-*/_search -k \
  -d '{"query": {"match": {"kubernetes.namespace": "production"}}}'
```

## Index Management

```Bash
# List all indices
curl -u elastic:PASSWORD https://localhost:9200/_cat/indices?v

# Delete old log indices to free space
curl -X DELETE -u elastic:PASSWORD https://localhost:9200/logstash-2024.01.*
```

Use Index Lifecycle Management (ILM) policies to automate retention and rollover.
