
The API server List+Watch is a client-server pattern for syncing resource state where clients will first LIST all matching objects to get a baseline snapshot and then WATCH for changes from the point forward via a persistent HTTP stream

### LIST Phase

The client sends `GET /api/v1/pods?limit=500&resourceVersionMatch=NotOlderThan` to the API server 

* The client tells the API server to return everything that currently exists within the cluster
* The API server will respond with matching objects along with a `resourceVersion` which is an `etcd` revision-like timestamp (Unique per change)

### WATCH Phase

The client immediately sends `GET /api/v1/namespaces/default/pods?watch=true&resourceVersion=10245 after receiving the API server's response in the LIST phase

* The client tells the API server to only return what changes were made from the previous phase
* The API server will hold the connection open and stream events using HTTP chunked transfer encoding over a long-lived HTTP GET request 

#### Event Types

| Event        | Meaning                                      | Contains                                |
| ------------ | -------------------------------------------- | --------------------------------------- |
| **ADDED**    | Object created after your `resourceVersion`  | Full object                             |
| **MODIFIED** | Object changed after your `resourceVersion`  | Full updated object                     |
| **DELETED**  | Object deleted after your `resourceVersion`  | Object as it was before deletion        |
| **BOOKMARK** | Progress marker if `allowWatchBookmars-true` | Metadata with current `resourceVersion` |
