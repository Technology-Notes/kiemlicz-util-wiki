# Basics
| Term | meaning |
|------|---------|
| minion | slave, managed host |
| sls | salt state file, representation of the state the system should end up in |
| pillar | sensitive data, tree like structure, targeted and securely send to selected minions |
| grain | minion data, information for state to behave differently (minion os information) |

## Architecture
Each state is enforced on targeted minion.
State during its execution uses modules. The difference between the two:

| State | Module |
|-|-|
| Assert/enforce certain state on minion. They map to nice `sls` files | Executes a task |

## Configuration
Master configuration: `/etc/salt/master`  
Master configuration overrides: `/etc/salt/master.d/myoverrides.conf`  
Minion configuration: `/etc/salt/minion`  

### Pillar
Pillar subsystem can be extended to fetch data from various sources, use `ext-pillar` to achieve this.  

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

### Evaluation order during execution
Jinja -> YAML -> highstate -> low state -> execution

# Usage
Execute states from given environment: `salt '*' state.highstate saltenv=base`  
Execute state: `salt '*' state.apply <statename> [saltenv=<env>]`

# salt-api
Using curl to communicate with API: `curl -sSk https://salt.local:9191/login -H 'Accept: application/x-yaml' -d username=saltuser -d password=saltpassword -d eauth=auto`

# References:
1. https://repo.saltstack.com/
2. https://docs.saltstack.com/en/getstarted/config/functions.html
3. https://docs.saltstack.com/en/latest/topics/tutorials/states_pt1.html
4. https://docs.saltstack.com/en/latest/ref/states/highstate.html
5. https://github.com/saltstack/salt-bootstrap
6. https://docs.saltstack.com/en/latest/ref/configuration/master.html
7. https://docs.saltstack.com/en/latest/topics/development/conventions/formulas.html