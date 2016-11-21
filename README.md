ansible-kvm
=========

Install qemu-kvm and dependencies on ubuntu 16.04
Create and delete VMs

Requirements
------------

Ubuntu 16.04
Not tested on redhat or fedora and wouldn't expect it to work as is

Role Variables
--------------

see `defaults/main.yml` for details.

Dependencies
------------

Not dependent on any other roles

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

VM definition
```
kvm_vms:
# dynamic IP
  - name: node0
    disk: 100
    cpu: 1
    ram: 1024
# static IP
  - name: node1
    disk: 100
    cpu: 2
    ram: 2048
    mac: 11:22:33:44:55:66
    ip: 1.1.1.1
```

Removing a VM definition from the list will remove the VM with `virsh destroy [name]`


License
-------

MIT

Author Information
------------------

rsys.io

