TEST: ansible-role-lxc
======================

This file describe the manual step to test the role.

## Setup

- Create a host for testing (a vm, a container, an instance on a cloud provider...)
- Update the `group_vars/lxchost-testvm.yml` file to configure your new host.

## Test1 : bootstrap

### Launch

    ansible-playbook -i inventory test.yml

### Results

It should:
 - connect to your host
 - install lxc
 - setup lxc-net
 - create 3 containers

*Note:* You have to verify all that manually.

## Test2 : idempotent

### Launch

    ansible-playbook -i inventory test.yml

### Results

It should:
 - not change anything.

