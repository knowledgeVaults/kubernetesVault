
## 1. Scheduling

* A Pod is added to the scheduling queue based on their priority 

| Extension Point | Description |
| --------------- | ----------- |
| `queueSort`     |             |

| Plugin        | Description |
| ------------- | ----------- |
| `PrioritySet` |             |

## 2. Filtering

* Eliminates unsuitable nodes using predicates (ex: *PodFitsResources*, *Node selectors*, *Taints and tolerations*, *Volume conflicts*, *etc.*)
* Done with the help of the `NodeResourcesFil`, `NodeName`, and `NodeUnschedulable` plugins

| Extension Point | Description |
| --------------- | ----------- |
| `prefilter`     |             |
| `filter`        |             |
| `postfilter`    |             |

| Plugin              | Description |
| ------------------- | ----------- |
| `NodeResourcesFit`  |             |
| `NodeName`          |             |
| `NodeUnschedulable` |             |
| `TaintToleration`   |             |
| `NodePorts`         |             |
| `NodeAffinity`      |             |

## 3. Scoring

* The scheduled and filtered pods are scored with weights
* Ranks feasible nodes via priority functions 
* The highest scorer wins but if nodes are tied, then it randomly picks one winner

| Extension Point | Description |
| --------------- | ----------- |
| `prescore`      |             |
| `score`         |             |
| `reserve`       |             |

| Plugin             | Description |
| ------------------ | ----------- |
| `NodeResourcesFit` |             |
| `ImageLocality`    |             |
| `TaintToleration`  |             |
| `NodeAffinity`     |             |

## 4. Binding

| Extension Point | Description |
| --------------- | ----------- |
| `permit`        |             |
| `preBind`       |             |
| `bind`          |             |
| `postBind`      |             |

| Plugin          | Description |
| --------------- | ----------- |
| `DefaultBinder` |             |