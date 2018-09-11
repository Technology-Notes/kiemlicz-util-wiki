# Configuration
Master configuration: `/etc/salt/master`  
Master configuration overrides: `/etc/salt/master.d/myoverrides.conf`  
Minion configuration: `/etc/salt/minion`  

## Pillar
Pillar subsystem can be extended to fetch data from various sources, use `ext-pillar` to achieve this.  

### file_tree
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