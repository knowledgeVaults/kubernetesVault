The **Job Controller** ensures that a specified number of Pods successfully complete their task, managing retries and parallelism

* Watches `Job` objects via the [[kube-apiserver]] [[List+Watch]] mechanism
* Creates Pods to fulfill the Job's completion requirements and tracks their outcomes
* Handles Pod failures by creating replacement Pods up to the `backoffLimit`

## Job Types

### Non-Parallel Job (default)

A single Pod is created and the Job is complete when that Pod exits successfully

```YAML
spec:
  completions: 1
  parallelism: 1
```

### Fixed Completion Count

The Job runs until the specified number of Pods complete successfully

```YAML
spec:
  completions: 5
  parallelism: 2
```

### Work Queue (Parallel, No Fixed Count)

Multiple workers process items from a shared queue; the Job completes when any Pod succeeds and all others have terminated

```YAML
spec:
  completions: null
  parallelism: 3
```

## Failure Handling

The Job Controller retries failed Pods up to `backoffLimit` (default 6) with exponential backoff

* A Pod is considered failed if it exits with a non-zero code or is marked `Failed` by the [[kubelet]]
* `activeDeadlineSeconds`: hard deadline for the entire Job; Pods are terminated and the Job fails if exceeded
* `backoffLimit`: maximum number of retry attempts before the Job is marked `Failed`

## Completion and Cleanup

```YAML
spec:
  ttlSecondsAfterFinished: 300
```

* `ttlSecondsAfterFinished`: automatically delete the Job and its Pods after the specified seconds post-completion
* Without TTL, completed Jobs accumulate and must be manually cleaned up

## Indexed Jobs

Indexed completion mode assigns each Pod an index (`JOB_COMPLETION_INDEX` env var) for partitioned workloads

```YAML
spec:
  completionMode: Indexed
  completions: 10
  parallelism: 3
```

## Suspend and Resume

Jobs can be suspended to pause execution without deleting the Job object

```YAML
spec:
  suspend: true
```

Changing `suspend` from `true` to `false` resumes the Job. All existing Pods are deleted when suspended and recreated on resume.
