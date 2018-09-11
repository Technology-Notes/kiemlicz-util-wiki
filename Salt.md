# Basics
| Term | meaning |
|------|---------|
| minion | slave, managed host |
| sls | salt state file, representation of the state the system should end up in |
| pillar | sensitive data, tree like structure, targeted and securely send to selected minions |
| grain | minion data, information for state to behave differently (minion os information) |

# Architecture
Highly [modular](https://docs.saltstack.com/en/latest/ref/index.html) and highly [customizable](https://docs.saltstack.com/en/latest/ref/modules/).
Event-based, runs in multiple modes: 
* master-slave
* master-less

Each _state_ is enforced on targeted minion (slave).  
State during its execution uses _modules_. The difference between the two:

| State | Module |
|-|-|
| Assert/enforce certain state on minion. They map to nice `sls` files | Executes a task |

Salt (as a whole) architecture consists of [multiple components](https://docs.saltstack.com/en/latest/topics/development/modular_systems.html) (also called modules):
 * runners: master-only command sequences
 * tops: builds top-file structure
 * pillar
 * grains
 * fileserver

## Evaluation order during execution
Jinja -> YAML -> highstate -> low state -> execution

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