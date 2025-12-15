PVE VM PATCHING
===============

Levrage ansible to upgrade guest virtual machines running in a Proxmox virtual environment.


Requirements
------------

- gather_facts: true
Ansible facts must be enable. Some conditions are using them to differenciate RHEL from Debian based OS.


Role Variables
--------------

The following variables must be defined. They are not mentioned in the defaults nor vars sections.

- vmid
The vmid will be use to snapshot the VM. It has to be defined per host.

All role variables are in the defaults section with comments.

The PVE API credentials are in the only variables in the vars section.

 
Dependencies
------------

Collections:
	- community.general
	- community.proxmox
	- containers.podman


Example Playbook
----------------

```
- name: Playbook name
  hosts: "host1:host2"
  become: yes
  gather_facts: true
  roles:
    - pve_vm_patching
```


Schedule
--------

As you see fit.


Error Handling
--------------

The initial task is ensuring that the vmid is defined per host.
After that all variables present in the defaults or vars section assume to be defined.
Some have default value (A comment say so).

The 'os patching' section is mandatory to run anything else.
Based on conditions, the follwing task will play or not.
 
If any patching task fail, it will not stop the play but issue an email notification.
Be sure to have a valid email and smtp host in the variable notification_email and smtp_host.
