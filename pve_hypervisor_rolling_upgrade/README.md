PVE HYPERVISOR UPGRADE
======================

Levrage ansible to upgrade proxmox hypervisors.
Upgrade each node one after the other by migrating the VM out.


Requirements
------------

- inventory_hostname == proxmox node id
Your nodes hostnames must match the node ID in your proxmox cluster.
'inventory_hostname' is used in the API call in place of the node ID.

- serial: 1 (Adapt to your cluster)
VM running on a host can be migrated to other nodes. Depending on your cluster size and the load, you should ajust the serial accordingliy.


Role Variables
--------------

All role variables are in the defaults section with comments. If they have a default value, it will be mentioned in a comment.

The PVE API credentials and SMTP info are in the only variables in the vars section.

The role can be play in 2 different way:
- Upgrade the node with VM running on it
	> Upgrade the node and check if a reboot is necessary
	> (If reboot) Migrate all VM out
	> (If reboot, If ceph set) Apply ceph flags
	> (If reboot) reboot
	> (If reboot) Migrate all VM back in
	> (If reboot, If ceph set) Remove ceph flags
	> Run post upgrade checks and email report
- Upgrade the node without any VM
	> Migrate all VM out
	> Upgrade the node and check if a reboot is necessary
	> (If reboot, If ceph set) Apply ceph flags
	> (If reboot) reboot
	> (If reboot, If ceph set) Remove ceph flags
	> Migrate all VM back in
	> Run post upgrade checks and email report

You can also force disable reboot, vm migration and ceph tasks through variables. (For standalone upgrade for example)


Dependencies
------------

Collections:
        - community.general
        - community.proxmox


Example Playbook
----------------

```
- name: Playbook name
  hosts: "host1:host2:host3"
  become: yes
  serial: 1
  gather_facts: false
  roles:
    - pve_hypervisor_rolling_upgrade
```


License
-------

BSD
