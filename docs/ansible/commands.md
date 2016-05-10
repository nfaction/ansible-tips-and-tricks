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

# Running Ansible as a different user

There are multiple ways to control which user account is used when executing Ansible.  It can be controlled via a user's `~/.ssh/config`, via `remote_user` in Ansible or through the Ansible inventory.

This has changed drastically between Ansible versions pre-2.0 and post 2.0

Follow this [link](http://docs.ansible.com/ansible/become.html#for-those-from-pre-1-9-sudo-and-su-still-work) to see how this can be done.

When running from the command line, one can just specify which user account to run against directly.  Please note that specifying a user can sometime conflict with a user defined in `/etc/ansible/hosts`

Specifying a user:

```
ansible-playbook playbooks/atmo_playbook.yml --user atmouser
```

Using a specific `SSH` private key:

```
ansible -m ping hosts --private-key=~/.ssh/keys/id_rsa -u centos
```

# Passing Variables via CLI

Variables can be pesky, but sometimes are required to be passed in via the CLI.  Any variable can be set via the command line.  Often the command line is the be all, end all in variable overrides.

Passing arguments:

```
ansible-playbook playbooks/atmo_playbook.yml -e "ATMOUSERNAME=atmouser"
```

# Limiting Playbook/Task Runs

When writing Ansible, sometimes it is tedious to make a change in a `playbook` or `task`, then run the `playbook`  It can sometimes be very helpful to run a module directly as shown above, but only against a single development host.

## Limit to one or more hosts
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

Limit to host group

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --limit 'group1'
```

## Limiting Tasks with Tags

Limit to all tags matching `install`

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --tags 'install'
```

Skip any tag matching `sudoers`

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --tags 'sudoers'
```

# Troubleshooting Ansible

<http://docs.ansible.com/ansible/playbooks_checkmode.html>

## Busted Cache
Sometimes Ansible has a tendency to hold on to variables too long, which causes Ansible to think that a task/operation had already been done or changed when in fact it didn't.

A simple fix is to flush the `redis` cache during a code execution.

This can be done like this:

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --flush-cache
```

## Check for bad syntax

One can check to see if code contains any syntax errors by running the playbook.

Check for bad syntax:

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --syntax-check
```

## Running a playbook in dry-run mode

Sometimes it can be useful to see what Ansible might do, but without actually changing anything.  

One can run in dry-run mode like this:

```
ansible-playbook playbooks/PLAYBOOK_NAME.yml --check
```

# When all else fails

## Modules
Sometimes Ansible just can't cut performing a task using the built-in modules.  Raw module to the rescue!

Using `raw` module to run command similar to running directly via `SSH`:

```
ansible -m raw -s -a "yum install libselinux-python -y" new-atmo-images
```

Other times, Ansible's modules either aren't well defined yet, or simply do not exist.  This is a use case for using `shell` and `command` modules.  More information for [shell](http://docs.ansible.com/ansible/shell_module.html) and [command](http://docs.ansible.com/ansible/command_module.html) modules.

The main differences between the two comes down to what kind of command one wishes to run.  If the command uses IO redirection of ANY sort, use `shell`.  If the command only contains CLI flags, `command` module will suffice.

## Checking Redis
Sometimes it may be neccessary to manually check Redis for gathered facts from a remote host.  Redis is the default back-end for Ansible and usually is running as a server under `127.0.0.1`.  One can check Redis by performing the following steps:

1. Log in to Ansible controller, if Ansible is not running on your local machine
1. Login to Redis
	
	```
	redis-cli
	```
	
1. List all stored entries for Ansible

	```
	KEYS ansible*
	```	

1. Find the entry for the remote host and display the gathered facts for that host

	```
	MGET "ansible_factsXXX.XXX.XXX.XXX"
	```

1. Exit Redis

	```
	quit
	```

## Write your own module
There is a wealth of modules available, and writing your own should only be done as an absolute last resort.

If you still wish to write your own, take a look at the docs here: <http://docs.ansible.com/ansible/developing_modules.html>
