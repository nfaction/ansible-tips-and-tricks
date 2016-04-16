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
	
1. Follow installation steps for both Ansible 2.0 and 1.9 [here.](docs/ansible/install.md)

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
	sudo su -
	vi /root/.ssh/authorized_keys
	<Insert your public key, save and exit>
	exit
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