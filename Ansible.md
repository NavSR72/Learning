# Ansible

## Ansible Configuration Files
`etc/ansible/ansible.cfg`

`ANSIBLE CONFIG=/opt/ansib1e-web.cfg ansible-playbook playbook.yml`

![image](https://github.com/user-attachments/assets/c39d5cb1-02a5-4a1e-955b-daf6be245bc5)

## Ansible Configuration Variables
#### For this cmd execution only
`ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml`

#### For the current shell session
`export ANSIBLE GATHERING=explicit`

#### For Persistent across sessions and users
`ansible-playbook playbook.yml`

```
/opt/web-playbooks/ansible.cfg
gathering = explicit
```

## View Configuration
 ```bash
$ ansible-config list # Lists all configurations
$ ansible-config view # Shows the current config file
$ ansible-config dump # Shows the current settings
$ export ANSIBLE GATHERING=exp1icit
ansible-config dump | grep GATHERING
```

Two types of Inventory formats are *.ini* and *.yaml*

