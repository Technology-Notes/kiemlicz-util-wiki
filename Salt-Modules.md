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
Salt supports two ordering modes:
 1. _Definition order_: in the order of appearance in `top.sls` file, thus it is the filename that determines order. Then the states are executed 
 by the order of appearance in the state file itself. The `include`d files that were defined later than including file are executed first, 
 `inculde` takes precedence. 
 2. _Lexicographic order_: states are sorted by: their _name_, _function_ and then by _state ID_. Enable with configuration option: `state_auto_order: False`, the `include` statement in `sls` files
 doesn't affect the order at all.

Both options respect the [requisite statements](https://docs.saltstack.com/en/latest/ref/states/ordering.html#requisite-statements).  
The requisites take precedence over configured ordering.

[Full list of requisite statements and their usage](https://docs.saltstack.com/en/latest/ref/states/requisites.html).

If multiple states happen to be assigned same order number (the internal number that formally determines execution order),
then _Salt_ fallbacks to _Lexicographic ordering_.

### [Evaluating states](https://docs.saltstack.com/en/latest/ref/states/compiler_ordering.html) 
By default _sls_ files are written using in YAML format with Jinja templates. Both engines are not aware of each other.
Jinja templating starts before validating YAML.  

In other words:
 1. Jinja must produce valid YAML file
 2. YAML file must be a valid [highstate](https://docs.saltstack.com/en/latest/ref/states/highstate.html#states-highstate-example) data structure
 3. Highstate is compiled to lowstate (lowstate is a sorted list of state executions)
 4. _Salt_ executes the list in the order

The full evaluation and execution order:  
`Jinja -> YAML -> highstate -> lowstate -> execution`

It is very easy to misuse jinja. When the state starts to be unreadable, it is possible candidate to switch to different 
renderer (usually `#!py`).  
However user desired logic may still be too complex. Then writing custom _Execution Module_ or _State Module_ is a better idea.  

Additionally as the _State Tree_ grows, it is easy to fall into following trap:
```
clone_repo:
  git.latest:
    - name: https://github.com/kiemlicz/util
    - target: /tmp/util/
    - branch: master

{% for f in '/tmp/util/' | list_files %}
{% if f == '/tmp/util/README.md' %}

add_developer_{{ f }}:
  file.append:
  - name: {{ f }}
  - text: "added contributor: bla@o2.pl"
  - require:
    - git: clone_repo
    
{% endif %}
{% endfor %}
```
User assumed that the jinja will 'see' `clone_repo` changes. It is not true.  
Jinja is evaluated first, thus if this sls file is applied first time, effectively the YAML looks like:
```
clone_repo:
  git.latest:
    - name: https://github.com/kiemlicz/util
    - target: /tmp/util/
    - branch: master
```
However during next runs, the jinja will 'see' the changes (as they are already applied). Thus the output YAML will become:
```
clone_repo:
  git.latest:
    - name: https://github.com/kiemlicz/util
    - target: /tmp/util/
    - branch: master

add_developer_/tmp/util/README.md:
  file.append:
  - name: /tmp/util/README.md
  - text: "added contributor: bla@o2.pl"
  - require:
    - git: clone_repo
```
There are couple of options how to overcome such situation, most common involve:
 - writing custom _Execution Module_ or _State Module_
 - use [Slots](https://docs.saltstack.com/en/latest/topics/slots/index.html)

#### Slots
Relatively new _Salt_ feature, allows to store the result of _Execution Module_ and use it in next _Modules_ (during same run).  
Example:  
```
dnsutils:
  pkg.latest:
  - name: dnsutils

find_domain:        # works
  cmd.run:
  - name: "nslookup google.com"

the_state_id:       # fails
  test.show_notification:
  - name: some name
  - text: "the server ip: {{ salt['cmd.run']("nslookup google.com") }}"
```
Will fail because not-yet-existing command output is used as part of state definition.  
Using slots we can overcome this:
```
dnsutils:
  pkg.latest:
  - name: dnsutils

find_domain:        # works
  cmd.run:
  - name: "nslookup google.com"

the_state_id:       # works
  test.show_notification:
  - name: some name
  - text: __slot__:salt:cmd.run("nslookup google.com")
```

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
Moreover adding jinja constructs that modify underlying system also doesn't help (not advised though).

However _Salt_ provides requisite constructs that can be added to (not all) states: `onlyif` or `unless`

Thus it is possible to make _Salt_ states idempotent 

### Best practices
Check these two documents, they provide excellent details about how to create state properly:
 - [best practices](https://docs.saltstack.com/en/latest/topics/best_practices.html)
 - [formulas best practices](https://docs.saltstack.com/en/latest/topics/development/conventions/formulas.html)

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
