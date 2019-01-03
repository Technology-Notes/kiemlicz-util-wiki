Ambiguous term in salt documentation, overused to describe any salt piece of logic

| State | Module |
|-|-|
| Assert/enforce certain state on minion. They map to nice `sls` files | Executes a single task, also known as execution modules |

State during its execution uses _modules_.

## Evaluation order during execution
Jinja -> YAML -> highstate -> low state -> execution


# Core modules
Modules that enforce state on the remote.
## execution modules

## state modules

# Data
 - grains
 - pillar
 - sdb
# Event
 
# Result
# Admin
# Integration
# Utility
