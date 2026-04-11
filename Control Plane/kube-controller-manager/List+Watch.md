The **[[List+Watch]]** pattern is the fundamental client-server mechanism Kubernetes uses to synchronise resource state between controllers and the [[kube-apiserver]]

* All controllers start by **listing** all current objects of a resource kind to build a local cache
* They then **watch** for incremental change events streamed from the API server
* This avoids polling and ensures controllers react to changes within milliseconds

## LIST Phase

The client sends a paginated GET request with `resourceVersionMatch=NotOlderThan`

```
GET /api/v1/pods?limit=500&resourceVersionMatch=NotOlderThan
```

* The API server returns matching objects from its in-memory watch cache
* The response includes a `resourceVersion` which a monotonically increasing [[etcd]] revision identifier
* The client stores all returned objects in its **informer cache** (local in-memory store)

## WATCH Phase

The client immediately opens a persistent watch stream from the `resourceVersion` received in the LIST phase

```
GET /api/v1/namespaces/default/pods?watch=true&resourceVersion=10245
```

* The API server keeps the connection open and streams events via HTTP chunked transfer encoding
* The client's informer updates its local cache for each event received

## Event Types

| Event | Meaning | Payload |
|---|---|---|
| `ADDED` | Object created after the watch's `resourceVersion` | Full object |
| `MODIFIED` | Object changed after the watch's `resourceVersion` | Full updated object |
| `DELETED` | Object deleted after the watch's `resourceVersion` | Object as it was before deletion |
| `BOOKMARK` | Progress marker (requires `allowWatchBookmarks=true`) | Metadata with current `resourceVersion` |
| `ERROR` | Watch expired or too old; client must re-LIST | Error object |

## Reconnection and Re-List

If the watch stream expires or the API server returns a `410 Gone` (watch too old):

1. The client discards its cache
2. Re-executes the full LIST phase to rebuild the baseline
3. Opens a new watch from the latest `resourceVersion`

This logic is handled automatically by the **client-go informer framework** used by all core controllers.

## Work Queue Pattern

Controllers do not process watch events directly as they enqueue the object key into a **work queue**

* The reconcile loop dequeues one key at a time and fetches the current state from the informer cache
* Retries with exponential backoff on transient errors
