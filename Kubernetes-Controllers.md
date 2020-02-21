Typically PODs are scheduled via higher-level abstraction that relies on `controller`s

# Deployment
Common method to create the groups of PODs with regards to number of instances running. "Replaces" `ReplicaSet`  

1. No POD location guarantees by default 
2. PODs will be suffixed with random string
3. They will be **started** at the same time 
4. PODs are updated one after another (previous one must be `READY` in order to continue) by default. 
   Refer to [`DeploymentStrategy`](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#deploymentstrategy-v1-apps)) for all options
   - `Recreate` - Terminate all, start all
   - `RollingUpdate`-  Don't let all PODs go down, upgrades gradually

# StatefulSet
Don't use if 3. is not required

1. No POD location guarantees by default
2. By default started one by one (previous one must be `READY` in order to continue). It is possible to start all at once
3. PODs have stable network identifier (predictable, ordered names instead of random hash). 
4. Updated in order (hi -> low), regardless of `podManagementPolicy` setting

# DaemonSet

1. POD location guarantees: one POD on each node
2. They will be started at the same time

# ReplicaSet
In general replaced by `kind: Deployment`. Can 'capture' other manually created PODs if they match labels

# Job

# Usage
For interaction with selected controller kind the `kubectl rollout` command is used.
The `.spec.template` must be changed in order to trigger update, otherwise the same deployment revision is used

| Information | Command |
|-|-| 
|Status | `kubectl rollout status <kind, e.g.: deployment, statefulset> <name>` |  
|History (The `CHANGE-CAUSE` is copied from annotation `kubernetes.io/change-cause`), more details can be obtained using `--revision`|`kubectl rollout history deployment <name> [--revision=N]`|
|Rollback|`kubectl rollout undo deployment <name>`|

Mind:
https://github.com/kubernetes/kubernetes/issues/82612 (change cause message doesn't work for `StatefulSet`)
