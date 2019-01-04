Ambiguous term in salt documentation, overused to describe any salt piece of logic

| State | Module |
|-|-|
| Assert/enforce certain state on minion. They map to nice `sls` files | Executes a single task, also known as execution modules |

State during its execution uses _modules_.

## Evaluation order during execution
Jinja -> YAML -> highstate -> low state -> execution

### Order of execution

### Idempotence
It is tempting to wonder: is the state execution idempotent?

Is depends, e.g., the states like:
```
run_scripts:
    cmd.script:
        - name: do_dangerous_stuff.sh
```
are not guaranteed to be idempotent because everything depends on underlying: `do_dangerous_stuff.sh` script, so in general it is wise
to assume that such state is not idempotent.  
However _Salt_ provides requisite constructs that can be added to (not all) states: `onlyif` or `unless`
The final answer: _Salt_ states should be made idempotent, e.g., using requisite constructs 

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
