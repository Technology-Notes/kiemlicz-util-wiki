Typically PODs are scheduled in groups

For interaction with selected controller kind the `kubectl rollout` command is used.

Check status with:  
`kubectl rollout status <kind, e.g.: deployment, statefulset> <name>`

Check deployment (for `deployment` kind) history with:  
`kubectl rollout history deployment <name>` 
The `CHANGE-CAUSE` is copied from annotation `kubernetes.io/change-cause`

To get more information about specific deployment, provide `--revision`, e.g.: `kubectl rollout history deployment <name> --revision=2`

Rollback revision:
`kubectl rollout undo deployment <name>`

The deplyment **yaml** file changes are not 'rollbackable', only `image` changes.

# Deployment
Common method to create the groups of PODs with regards to number of instances running.  

1. PODs will be suffixed with random string
2. No POD location guarantees by default
3. They will be started at the same time 
4. PODs are updated one after another

# StatefulSet

1. No POD location guarantees by default
2. Started one by one (previous one must be 'READY')
3. PODs have stable network identifier
4. Updated in order (hi -> low)

# DaemonSet

1. POD location guarantees: one POD on each node
2. They will be started at the same time

# ReplicaSet
In general replaced by `kind: Deployment`. Can 'capture' other manually created PODs if they match labels
