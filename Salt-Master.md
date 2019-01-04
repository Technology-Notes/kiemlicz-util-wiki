Responsible for managing the minions.  
Provides the states and the pillar data.
States are send and rendered on minions, pillars are send and cached on minions.  
In order to match which states and which pillar data should be send to given minions the [targeting](https://docs.saltstack.com/en/latest/topics/targeting/) is used. 

## Targeting and Top file
Targeting is specifying which minions should execute given state as well as contain given pillar data.

Typically targeting is contained in [`top.sls`](https://docs.saltstack.com/en/latest/ref/states/top.html) file.  
```
base:              # environment 
  'minion_id':     # target
    - some_state   # states
```
`top.sls` consists of three parts:
 - environment (_saltenv_) - main one, contains targets
 - target - contains states
 - states

### Environments aka saltenv
Most often used to isolate operations within different... environments, e.g., `dev`, `test`, `prod`.   
Requires [additional configuration]()   
Helps to:
 - Group minions by their role
 ```
 prod:
   'prod*':
     - some_state
 ```
 However it is possible to achieve this without multiple environments (by adding proper grains on minions):
 ```
 base:
   'role:prod':
     - match: grain
     - some_state
 ```
 - Group states by their purpose. Helps to organize the states
 
### Targeting
By default the shell-style globbing on minion id is used, e.g.: `salt 'minion_id' test.ping` or
```
base:
  'minion_id':
    - some_state
```
[List of all targeting options](https://docs.saltstack.com/en/latest/topics/targeting/)

#### Targeting with grains
Grains are uploaded upon first contact and in general should not change, thus the grain targeting is safe.  
`salt -G 'your:grain:path:value' test.ping`, .e.g.,: `salt -G 'os:Debian' test.ping`

#### Targeting with pillar
Pillar is actually cached not only on _Salt Minion_ but on _Salt Master_ too, in order to use pillar targeting, the
pillar data must be refreshed on master: `salt '*' saltutil.pillar_refresh`  
`salt -I 'some:pillar:value' test.ping`, or `salt -I 'some:pillar:value_prefix*' test.ping`

#### Compound targeting
Allows to mix all of the options using slightly different syntax:  
`salt -C 'G@os:Debian and I@redis:setup_type:cluster' test.ping` 
Find all of the available prefixes to be used [here](https://docs.saltstack.com/en/latest/topics/targeting/compound.html#targeting-compound)

## Pillar
Based on everything the _Salt Master_ knows about the minion and minion grain data the _Salt Master_ creates 
the pillar data and sends it over to minion.
Secrets can be placed in pillar data
It is possible to include _Salt Master_ configuration files in the pillar data. 
In `/etc/salt/master.d/custom.conf` setting: `pillar_opts: True` controls this.

### Environments aka. pillarenv

### CLI
Inspect minion pillar data: `salt 'my_minion' pillar.items`
