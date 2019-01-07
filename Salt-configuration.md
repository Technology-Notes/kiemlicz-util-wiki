# Configuration
Written in YAML files, located in `/etc/salt/master` and `/etc/salt/master.d/`  
Master configuration: `/etc/salt/master`  
Master configuration overrides: `/etc/salt/master.d/myoverrides.conf`  
Minion configuration: `/etc/salt/minion`  
Minion configuration overrides: `/etc/salt/minion.d/myoverrides.conf`
Overrides **require** `.conf` suffix, otherwise they are not included and nothing is logged.  

## States
Defined using `*.sls` files, located in state directories listed in `file_roots` configuration option (`/srv/salt/` by default).  
The state directories are also called _State Tree_  
Example of state file contents:
```
funny_user_setup:                 # state ID
  user.present:                   # <state module>.<function>
    - name: cool_guy              # <function's> arguments
    - fullname: Cool Guy Jr
    - shell: /bin/bash
    - home: /home/cool_guy
```
Given the above `file_roots` configuration this state file must be located in `/srv/salt/the_state.sls` or `/srv/salt/the_state/init.sls`, 
(the `init.sls` is treated as parent directory).  
The sls filename cannot contain dots (other than suffix `.sls`). Filename with dots is expanded by salt as directories, e.g.,
`the_state.something.sls` will be understood as `/srv/salt/the_state/something.sls`.  
When both `/srv/salt/the_state.sls` or `/srv/salt/the_state/init.sls` exist, the `the_state/init.sls` is ignored.   
By default the sls file represents python dictionaries, lists, strings and numbers only.

Salt also requires special file to exist in top level of _State Tree_. This special file is responsible for matching states to actual machines.
It is called `top.sls`, example:
```
base:
  'machine':
    - the_state
```
For more information about Top File structure and state definition refer to [Targeting and Top file](https://github.com/kiemlicz/util/wiki/Salt-Master#targeting-and-top-file)

## Pillar
Defined in similar way as state files, located in directories listed in `pillar_roots` configuration option.
There is no enforced pillar data syntax, given default renderer the pillar must be just provided as YAML file.  
The `top.sls` file is also expected to exist in `pillar_roots` top directory.
Example pillar data file:
```
some_user_data:
  mail: cool_guy@o2.io
  home_dir: /home/cool_guy
```

### External pillar
Pillar subsystem can be extended to fetch data from various sources, [use `ext_pillar` to achieve this](https://docs.saltstack.com/en/latest/topics/development/external_pillars.html).
The external pillar must be configured in master configuration first.

#### git_pillar

#### file_tree
`ext pillar: file_tree`
File becomes the value of key  
Under the `root_dir` you must have either `hosts/minion_id` folder or `nodegroups/nodegroup` folder  
Data is available to matching hosts.  
Example configuration:  
```
ext_pillar:
  - file_tree:
      root_dir: /path/to/root/directory
      keep_newline:
        - files/testdir/*
```

## Dunder dictionaries
_Salt_ modules are 'wrapped' within special _dunder_ dictionaries. These special dictionaries provide access to _Salt_ **different** modules.

| Dictionary name | Available in | Information |
|-----------------|--------------|-------------|
| \_\_OPTS\_\_ | Loader modules | configuration file contents |
| \_\_SALT\_\_ | Execution, State, Returner, Runner, SDB modules | **execution** modules |
| \_\_GRAINS\_\_ | Execution, State, Returner, External Pillar modules | minion grains data |
| \_\_PILLAR\_\_ | Execution, State, Returner modules | pillar data |
| \_\_CONTEXT\_\_ | Execution, State modules | all-purpose dict, that exists during all state runs |

### Using dunder dictionaries
Jinja2+YAML:
```
apache:
  pkg.installed:
    - name: {{ __salt__['pillar.get']('pkgs:apache', 'httpd') }}
```
  
```
{% for mnt in __salt__['cmd.run']('ls /dev/data/moose*').split() %}
/mnt/moose{{ mnt[-1] }}:
  mount.mounted:
    - device: {{ mnt }}
    - fstype: xfs
    - mkmnt: True
{% endfor %}
```

# Extending Salt
It is very easy to create your own _Salt_ modules or even alter existing ones. All of the module changes must be synchronized
to minions prior to use (this happens automatically only for `state.highstate` call). By default, extensions must be placed in directories in _State Tree_
root, following naming convention: `_<module_type>`.  
It is also possible to define custom extensions in different places, given proper `extension_modules` configuration.
