Ansible uses a combination of a `hosts` file and a `group_vars` directory to pull variables per host group and run Ansible plays/tasks against hosts.

`group_vars/all` is used to set variables that will be used for every host that Ansible is ran against.

More information can be found here: <http://docs.ansible.com/ansible/intro_inventory.html>

# Hosts file

A `hosts` file consists of host groups and hosts within those groups.  A super-set of hosts can be built from other host groups using the `:children` operator.

Below is an example of a very basic Ansible hosts file.  Be aware that there are major differences between Ansible 2.0 and versions below it.

These files can be found within a Ansible repository or in `/etc/ansible/hosts`

## Ansible 1.9 and lower
```
---
[back-end-and-infra:children]
back-end-servers
infrastructure

[back-end-servers]
database ansible_ssh_host=10.0.0.1 ansible_ssh_port=22 ansible_ssh_user=postgres
webserver ansible_ssh_host=10.0.0.2 ansible_ssh_port=22 ansible_ssh_user=root

[infrastructure]
ldap ansible_ssh_host=10.0.0.100 ansible_ssh_port=22 ansible_ssh_user=root
```

## Ansible 2.0
The main difference is the removal of the `_ssh_` middle section of the host entry.

```
---
[back-end-and-infra:children]
back-end-servers
infrastructure

[back-end-servers]
database ansible_host=10.0.0.1 ansible_port=22 ansible_user=postgres
webserver ansible_host=10.0.0.2 ansible_port=22 ansible_user=root

[infrastructure]
ldap ansible_host=10.0.0.100 ansible_port=22 ansible_user=root
```

# Group_vars

As mentioned above, `all` applies to any machine Ansible is ran against.  If your deployment requires specific variables in addition to the already defined vars, one can specify others based on host groups.

## All
```
---
# <ansible_base_dir>/group_vars/all

SSH_PORT: 22

ADMIN_USER: adminz_rule

```
## Group Specific Variables
```
---
# <ansible_base_dir>/group_vars/back-end-servers

WEBSERVER:
  PORT: 8080
  WEBROOT: /var/www
  
DATABASE:
  USER: admin
  PORT: 3306
  
PATH_ENV: /opt/dev

```

```
---
# <ansible_base_dir>/group_vars/infrastructure

ADMIN_LDAP_GROUP: admin
SERVER: ldap.domain.com

```