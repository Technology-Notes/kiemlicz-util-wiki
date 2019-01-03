Salt is primarily a configuration management solution and remote execution engine.  
Thanks to the latter, many use-cases can (but not necessarily should) be covered using just this one tool.

# Basics
| Term | meaning |
|------|---------|
| minion | slave, managed host |
| sls | _salt state file_, file that represents the state the system should end up in |
| pillar | sensitive data, tree like structure, targeted and securely send to selected minions |
| grain | minion data, information for state to behave differently (minion os information) |

# Architecture
Event-based, highly [modular](https://docs.saltstack.com/en/latest/ref/index.html) and highly [customizable](https://docs.saltstack.com/en/latest/ref/modules/).
Runs: 
* master-slave
* master-less

Supports both management modes:
* push (the config server sends the config updates to slaves)
* pull (the slaves check server for config updates)

Can operate:
* with agents
* [agent-less](https://docs.saltstack.com/en/latest/topics/ssh/)

States are matched to minions using (primarily) [`top.sls` file](https://docs.saltstack.com/en/latest/ref/states/top.html).  
Each _state_ is then executed on targeted minion (slave).  

Full state execution details on minion nodes is described in [Modules](https://github.com/kiemlicz/util/wiki/Salt-Modules) section

# References:
1. https://repo.saltstack.com/
2. https://docs.saltstack.com/en/getstarted/config/functions.html
3. https://docs.saltstack.com/en/latest/topics/tutorials/states_pt1.html
4. https://docs.saltstack.com/en/latest/ref/states/highstate.html
5. https://github.com/saltstack/salt-bootstrap
6. https://docs.saltstack.com/en/latest/ref/configuration/master.html
7. https://docs.saltstack.com/en/latest/topics/development/conventions/formulas.html
8. https://vimeo.com/289106306/7fd5601ce6