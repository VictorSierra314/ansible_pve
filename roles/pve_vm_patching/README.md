RHEL Patching
=============

Levrage ansible to path RHEL host unattended.

Requirements
------------

The variable batch must be defined when running the playbook.
If not the play will exit on failure.
```
ansible-playbook /path/to/playbook.yml -e batch=batchletter
```
batch value are: A, H, I, J, K, L, test.

https://moneris365.sharepoint.com/:x:/r/sites/MFSTech/sys_admins/Shared%20Documents/Servers/Server%20Reference%20SME%20and%20delegation.xlsx?d=w65d27fbd3fc742c9b8c4e7347f176d7f&csf=1&web=1&e=GRwHpb

Role Variables
--------------

Defaults
- notification_email: #Any email here, will receive the playbook alert and notifications
- batch: #HAS TO BE DEFINE WHEN RUNNING THE PLAYBOOK, accepted values: A, H, I, J, K, L
- log_path: #Log path. Must be the folowing format: "{{ log_path }}_batch{{ batch }}_{{ ansible_date_time.date }}.log"
- print_patching_output: #boolean, print yum update output or not
- print_autorm_output: #boolean, print yum autoremove output or not

Vars
- service_check_exceptions: #List here any host that will not run the service check post update

Dependencies
------------

roles:
- satellite_cv_publish_and_promote

Both roles will work independently but it wont patch anything if satellite content views are not updated prior to patching.

Current schedule publish and promote related content views and environment the day prior to patching

Example Playbook
----------------

```
- name: RHEL Patching automation - Batch{{ batch }}
  hosts: "batch{{ batch }}"
  become: yes
  gather_facts: true
  roles:
    - pve_vm_patching
```
The variable batch is mandatory when running the playbook.
It can be defined in the playbook itself with:
```
  vars:
    batch: A
  roles:
    - pve_vm_patching
```
Or in the playbook command line:
```
/bin/ansible-playbook /root/ansible_data/playbooks/Patching/patching.yml -e batch=A
```

Schedule
--------

Schedule is a couple of cron, one for each batch

/etc/cron.d/ansible_pve_vm_patching

Each cron send ansible output to a log file. The log file path and name must match the log_path variable to received it by email with the report.

Error Handling
--------------

The first task 'os patching' is mandatory to run anything else.
Based on conditions, the follwing task will play or not.
 
If any patching task fail, it will not stop the play but issue an email notification.
Be sure to have a valid email in the variable notification_email.
