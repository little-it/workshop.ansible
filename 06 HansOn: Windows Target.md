# Ansible Windows Targets
For sure you can reach Windows Systems as well.
* Do you remember how this works?
	* Explain it to the class
* What prerequisites do we need to cover?

## Minimal Windows Playbook Setup
### Prepare Windows VM
* Setup a current Windows OS
* Create a Snapshot of the VM
* Create local admin user
* Configure WinRM
	* https://docs.ansible.com/ansible/latest/user_guide/windows_setup.html 

### Prepare Control Node
* https://www.ansible.com/blog/connecting-to-a-windows-host
```bash
pip3 install pywinrm
```
### Create Ansible Project Files

#### Project
```bash
PNAME="Windows_WinRM"
PDIR="/etc/ansible/projects/win_demo"
mkdir -p $PDIR
chmod 700 $PDIR
```
#### Inventory
* <code>$PDIR/inventory</code>
```ini
# ansible demo inventory for $PNAME
[win_hosts]
win1

[win_hosts:vars]
ansible_user=loc_adm
ansible_password=ChangeMe...
ansible_connection=winrm
# ansible_winrm_server_cert_validation=ignore
```

#### Ansible Config
* <code>$PDIR/ansible.cfg</code>
```ini
# custom ansible $PNAME configuration
[defaults]
inventory      = ./inventory
roles_path    = ./roles
collections_paths = ./collections
remote_user = root
log_path = ./ansible.log
```

#### Playbook
* <code>$PDIR/win_update.yml</code>
```yaml
---
- name: Update with Important Windows Patches
  hosts: win_hosts
  tasks:
  - name: Apply Patches
    win_updates:
      category_name:
      - SecurityUpdates
      - CriticalUpdates
      blacklist:
      - Windows Malicious Software Removal Tool for Windows
      - \d{4}-\d{2} Cumulative Update for Windows Server 2016
      reboot: yes
      reboot_timeout: 1800
    until: not update_results.changed
    retries: 10
    delay: 5
    register: update_results

  - name: print update results
    debug:
      var: update_results
...
```

#### Run the Update
Just do it and look what happens on the Windows Hosts Console.

### Hints
* Shell Ping?
* WinRM?
* Ansible Ping?
* Vars?
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzQ2NzgzMzk1LDE1NDYyNjYwNDgsLTQ4Nz
g4MDgzMiwtNjIxNTA5MTc2LDE1NjA4MzEyODMsNzA1NTE0MjQ2
LC0xMTM4MjU2ODIzLC0yMTQ3MDUzOTYsLTc4NjMzNTMyOSwyNT
M1NjE3MzUsOTgyMTkzMjM1LDYxMzIzNTU0OSwxNzUyMDExNTcx
XX0=
-->