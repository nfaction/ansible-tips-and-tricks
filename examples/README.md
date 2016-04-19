# Ansible Examples

The examples presented here may have requirements on which version of Ansible is used, such as version 2.0 versus 1.9.  The simplest way to begin running these examples is to use a Python VirtualEnv and drop into the `ansible` directory here.

## Installing Ansible Here

From this directory, follow the instructions to install Ansible with Python Pip, then activate the environment and start running code.

Steps to install:

1. Download this repository

	```
	git clone https://github.com/cyverse/ansible-tips-and-tricks.git
	```
	
1. Navigate to the `examples` directory

	```
	cd ansible-tips-and-tricks/examples
	```
	
1. Follow installation steps for both Ansible 2.0 and 1.9 [here.](../docs/ansible/install.md)

1. Activate the Ansible Environment

	```
	source ansible<version2.0_OR_1.9>/bin/activate
	```
	
1. Run example playbooks

	```
	ansible-playbook playbooks/<playbook>.yml -i hosts
	```
	
1. Deactivate the environment

	```
	deactivate
	```
	
## Don't have a box to run Ansible against?

Feel free to use `vagrant` to run a instance that can be used to deploy code.

Follow these steps to get started:

1. Install Virtualbox
1. Install Vagrant: <https://www.vagrantup.com/docs/installation/>
1. Start up Vagrant

	```
	cd vagrant
	vagrant up
	vagrant ssh
	sudo vi /root/.ssh/authorized_keys
	<Insert your public key, save and exit>
	exit
	cd ..
	```
	
## Verify that Ansible is configured
First ensure that the environment is set up and running and able to run these examples.  If you are planning to use a host other than the `vagrant` host provided, be sure to uncomment and set the IP and Username for the `remote_host` entry.

Perform a simple `ping` test

```
ansible examples -m ping -i hosts
```

You should see this if things worked correctly:

```
vagrant-box | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

If you do not see the above, but see this:

```
vagrant-box | UNREACHABLE! => {
    "changed": false,
    "msg": "SSH Error: data could not be sent to the remote host. Make sure this host can be reached over ssh",
    "unreachable": true
}
```

check your ssh keys and try again.

## Run NTP Example

This example installs and configures NTP on your instance.

Ensure that your Ansible environment has been loaded, and your `vagrant` box is "up" before running the steps below

Run the `ntp.yml` playbook:

```
ansible-playbook playbooks/ntp.yml -i hosts
```

You should see something similar to this:

```
(ansible2.0):examples $ ansible-playbook playbooks/ntp.yml -i hosts

PLAY [install and configure NTP] ***********************************************

TASK [setup] *******************************************************************
ok: [vagrant-box]

TASK [ntp : gather os specific variables] **************************************
ok: [vagrant-box] => (item=ansible-tips-and-tricks/examples/roles/ntp/vars/Ubuntu.yml)

TASK [ntp : install ntp package] ***********************************************
changed: [vagrant-box]

TASK [ntp : verify that ntp is installed] **************************************
ok: [vagrant-box]

TASK [ntp : update system date manually] ***************************************
skipping: [vagrant-box]

TASK [ntp : update hardware clock manually] ************************************
skipping: [vagrant-box]

TASK [ntp : configure ntp] *****************************************************
changed: [vagrant-box] => (item=ansible-tips-and-tricks/examples/roles/ntp/templates/Ubuntu.ntp.conf.j2)

TASK [ntp : start ntp service] *************************************************
ok: [vagrant-box]

PLAY RECAP *********************************************************************
vagrant-box                : ok=6    changed=2    unreachable=0    failed=0
```

Verify that NTP is actually running and installed:

```
ssh -p 2222 root@127.0.0.1 "date && ntpq -p"
```

You should see something similar to this:

```
Sat Apr 16 01:21:57 UTC 2016
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 colonialone.pqx 216.218.254.202  2 u    9   64    1   32.149    1.549   0.000
 ha2.smatwebdesi 130.207.244.240  2 u   10   64    0    0.000    0.000   0.000
 nox.prolixium.c 200.98.196.212   2 u    8   64    1   49.765    5.063   0.000
 unlawful.id.au  131.107.13.100   2 u    7   64    1   33.771   10.052   0.000
 golem.canonical 140.203.204.77   2 u    8   64    1  153.101    3.706   0.000
```

If you are done, go ahead and take down and destroy your `vagrant` box
 
```
cd vagrant
vagrant destroy -f
cd ..
deactivate
```
 
