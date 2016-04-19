# Running Ad Hoc Commands

The basic syntax consists of `ansible` then the host group from `hosts` to run against, `-m <MODULE_NAME>`, and optionally providing arguments via `-a "OPT_ARGS"`

## Simple Modules

Ping hosts
  
```
ansible <HOST_GROUP> -m ping
```

Display gathered facts
  
```
ansible <HOST_GROUP> -m setup | less
```

Filter gathered facts
  
```
ansible <HOST_GROUP> -m setup -a "filter=ansible_distribution*"
```

## Advanced Modules
  
Copy SSH key manually

```
ansible <HOST_GROUP> -m authorized_key -a "user=root key='ssh-rsa AAAA...XXX == root@hostname'"
```
	
Modify file using `lineinfile`

```
ansible all -m lineinfile -a "dest=/etc/group regexp='^(users:x:100:)(.*)' line='\1ldapusername,\2' state=present backrefs=yes"
```

# Troubleshooting Modules

When writing Ansible, sometimes it is tedious to make a change in a `playbook` or `task`, then run the `playbook`  It can sometimes be very helpful to run a module directly as shown above, but only against a single development host.

## Limiting Hosts

This is required when one wants to run a `playbook` against a host group, but only against one or more members of that group.

Limit to one host

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --limit "host1"
```
	
Limit to multiple hosts

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --limit "host1,host2"
```

Negated limit. **NOTE:** Single quotes MUST be used to prevent bash interpolation.

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --limit 'all:!host1'
```