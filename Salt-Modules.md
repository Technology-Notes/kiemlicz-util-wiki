Ambiguous term in salt documentation, overused to describe any piece of salt logic.  
The `*.sls` files contains state definitions, this is just the data file (YAML by default) the Salt translates into _State Module_ execution

# Core Modules 
## Execution Modules
[salt/modules](https://github.com/saltstack/salt/tree/develop/salt/modules)  
Custom: `salt://_modules`  
The `salt` or `salt-call` command executes exactly the _Execution Modules_  
Contrary to _State Modules_, _Execution Modules_ don't check any state, they just perform action.

`salt '*' cmd.run "ls /" cwd=/`  

`cmd` - the module name, the module name is either the `salt/modules/cmd.py` or `salt/modules/any_name.py` containing `__virtualname__ = "cmd"`  

`run` - the actual function definition, everything after function definition is the args and kwargs (for the actual function or for the salt state compiler)

`"ls /"` - first positional argument of [run function](https://docs.saltstack.com/en/latest/ref/states/all/salt.states.cmd.html#salt.states.cmd.run)

`cwd=/` - keyword argument

## State Modules
[salt/states](https://github.com/saltstack/salt/tree/develop/salt/states)  
Custom: `salt://_states`  
Enforces desired states on the remote. Under the hood they usually use _Execution Modules_

```
pkgs_pip:
  pip.installed:
    - name: pip_packages
    - pkgs:
      - google-auth
    - reload_modules: True
```

`pkgs_pip` - state ID

`pip` - _State Module_ name, maps directly to either `salt/states/pip.py` or `salt/states/any_name.py` containing `__virtualname__ = "pip"`

`installed` - the actual function name, naming convention: past tense.

`- name: pip_packages` the first positional argument, by convention called `name`. When the `name` is not passed the state ID is used as the `name`  

### Ordering states
Salt supports two ordering styles:
 1. Lexicographic, states are sorted by their state ID, set `state_auto_order: False`
 2. 


### Evaluation order during execution
Jinja -> YAML -> highstate -> low state -> execution


#### Idempotence
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


Modules that enforce state on the remote.

# Data modules
Contains runtime configuration, variables, secret data... data...

## Grains modules
## Pillar modules
## SDB modules

# Event 
# Result
# Admin
# Integration
# Utility
