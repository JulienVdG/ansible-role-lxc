ansible-role-lxc
================

LXC host and containers setup through SSH to host

Requirements
------------

The ssh connection to the host should be setup.

Role Variables
--------------

### Variables for default lxc configuration:

`lxc_bridge_name`: the bridge to use for creating containers,
                   default to `lxcbr0`.

### Variables for lxc-net configuration:

`lxc_net_enable`: setup and start `lxc-net` service, default to true.

`lxc_net`: configuration for `lxc-net`. Example:

```
lxc_net:
  bridge: '{{ lxc_bridge_name }}'
  address: 10.0.3.1
  netmask: 255.255.255.0
  network: 10.0.3.0/24
  dhcp_range: 10.0.3.2,10.0.3.254
  dhcp_max: 253
  dhcp_confile: ""
  domain: ""

```

### Variables for containers creation

`lxc_group_prefix`: prefix for the lxc groups, see examples bellow,
                    default to "lxchost-".

`lxc_template`: template to use for the containers,
                default to "debian".
                Can be overriden per continainer by the hostvars of the same name `lxc_template`.


`lxc_template_options`: associate default options for each templates.
                        Ensure that the container has `sudo` and `python3`
                        Can be overriden per container by the hostvars of the
                        same name `lxc_template_options`.

`lxc_container_config`: associate default lxc configuration for each templates.
                        Can be overriden per container by the hostvars of the
                        same name `lxc_container_config`.

_Notes_:
 - The variable `{{ cn }}` is set to the container name.
 - The hostvars `lxc_vethpair` can be used to override the default `lxc.network.veth.pair` limited to 16 chars.

Dependencies
------------

n/a

Example Playbook
----------------

```
- hosts: lxc-hosts
  become: yes
  roles:
    - { role: ansible-role-lxc }
```

Example Inventory
-----------------

```
[lxc-hosts]
# here list all hosts where to setup lxc
testvm

[lxchost-testvm]
# have a group per host to configure all the containers running on the host
testvm #the host itself is in the group as is share the ssh params
# the containers must have the container_name set
mycontainer1 container_name=mycontainer1 lxc_vethpair=vethmyc1
appserver1 container_name=appserver1
webserver1 container_name=webserver1
```

Example Group Vars
------------------
As the host and containers share the same ssh parameters, gather them in a group.

#### lxchost-testvm.yml
```
---
ansible_host: '192.168.122.91'
ansible_user: 'ansible-admin'

physical_host: '192.168.122.91' # this is required by the connection plugin
```

#### all.yml
```
---
# containers are installed with python3
# see https://docs.ansible.com/ansible/latest/reference_appendices/python_3_support.html
ansible_python_interpreter: "/usr/bin/python3"
```

Configure connection plugin
---------------------------

If you want to use the connection plugin without running the role
(ie for a playbook to only setup inside containers)
you can add it to your `ansible.cfg`, example:
```
[defaults]
connection_plugins = ../roles/ansible-role-lxc/connection_plugins
```

License
-------

Copyright 2018-2019 Julien Viard de Galbert

Licensed under the Apache License, Version 2.0 (the "License"); you may
not use this file except in compliance with the License. You may obtain
a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations
under the License.

Credits
-------

I want to credit the following sources that inspired me in writing this module:

- The `lxc-net` setup is inspired from the one in [LXC playbook](https://github.com/deimosfr/ansible-lxc) by Pierre Mavro / deimosfr.

  The [debian wiki on LXC SimpleBridge](https://wiki.debian.org/LXC/SimpleBridge) was also really useful.

- The lxc through host ssh connection plugin is adapted from the ssh connection plugin from [OpenStack-Ansible plugins](https://github.com/openstack/openstack-ansible-plugins.git). The main change is permitting host connection as non-root and using `become` to call `lxc-attch`.

- The containers creation loop idea is inspired from [boot LXC](https://github.com/peopledoc/ansible-boot-lxc)by James Pic, adapted to the ssh host instead of local host and reusing variables as defined by the connection plugin.

Author Information
------------------

Julien Viard de Galbert

http://silicone.blogsite.org/

