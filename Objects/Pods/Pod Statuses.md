**Pod Statuses** describe the current phase and condition of a Pod as observed by the [[kubelet]] and the [[kube-apiserver]]

## Pod Phases

The `status.phase` field represents the high-level lifecycle state of a Pod

| Phase | Description |
|---|---|
| `Pending` | Pod accepted by the cluster but not yet running; waiting for scheduling or image pull |
| `Running` | At least one container is running, or is starting/restarting |
| `Succeeded` | All containers have terminated successfully (exit code 0); applies to Jobs |
| `Failed` | At least one container terminated with a non-zero exit code |
| `Unknown` | Pod state cannot be determined — usually because the node is unreachable |

## Pod Conditions

`status.conditions` provides finer-grained state information

| Condition | Meaning |
|---|---|
| `PodScheduled` | Pod has been assigned to a node |
| `Initialized` | All init containers have completed successfully |
| `ContainersReady` | All containers in the pod are ready |
| `Ready` | Pod is ready to receive traffic (gates service routing) |

## Container States

Each container within a Pod reports one of three states

| State | Description |
|---|---|
| `Waiting` | Container not yet running; `reason` explains why (e.g., `ContainerCreating`, `CrashLoopBackOff`) |
| `Running` | Container is executing |
| `Terminated` | Container has exited; includes `exitCode` and `reason` |

## Common Reasons for Pending/Waiting

| Reason | Cause |
|---|---|
| `Unschedulable` | No node satisfies the Pod's resource requests, affinity rules, or taints |
| `ContainerCreating` | Image is being pulled or volumes are being mounted |
| `CrashLoopBackOff` | Container repeatedly crashes and is backing off restart |
| `ImagePullBackOff` | Image cannot be pulled (bad tag, missing credentials) |
| `InvalidImageName` | Image name is malformed |
| `OOMKilled` | Container exceeded its memory limit and was killed by the OOM killer |

## Checking Pod Status

```Bash
kubectl get pod POD_NAME -o wide
kubectl describe pod POD_NAME       # Shows conditions and container state details
kubectl get events --field-selector involvedObject.name=POD_NAME
```
