# CyVerse Ansible Hackathon
## Pre-reqs
### Mac
1. Install Virtualbox

	```
	brew install Caskroom/cask/virtualbox
	```

1. Install Vagrant

	```
	brew install Caskroom/cask/vagrant
	```

### Linux
1. Install Virtualbox: <http://www.howopensource.com/2013/04/install-virtualbox-ubuntu-ppa/>

	```
	sudo apt-get install dkms
	wget -q http://download.virtualbox.org/virtualbox/debian/oracle_vbox.asc -O- | sudo apt-key add -
	sudo sh -c 'echo "deb http://download.virtualbox.org/virtualbox/debian trusty contrib" >> /etc/apt/sources.list.d/virtualbox.list'
	sudo apt-get update
	sudo apt-get install virtualbox-5.0
	```

1. Install Vagrant

	```
	sudo apt-get install vagrant
	```

## Basic Ansible Demo using Vagrant

1. Clone this repo and get started:

	```
	git clone https://github.com/cyverse/ansible-tips-and-tricks.git
	cd ansible-tips-and-tricks/
	cd examples/vagrant
	vagrant up
	```
	
1. Change into Ansible Deployment directory

	```
	cd ../
	```
	
1. Create Ansible VirtualEnvs for 2.0 and 1.9 as shown [here.](docs/ansible/install.md)

1. Now that your Vagrant box is up, set up SSH keys

	```
	# Test logging in via SSH
	ssh -p 2222 vagrant@127.0.0.1
	# Enter password: 'vagrant'
	exit
	
	# Copy in SSH keys
	ansible-playbook playbooks/copy_ssh_keys.yml -i hosts --limit single-ubuntu --ask-pass
	
	# Test SSH login as 'root'
	ansible examples -m ping -i hosts --limit single-ubuntu
	```