# Ansible Examples

These examples are designed to show you the basic layout of Ansible roles and act as a way to "kick the tires" using Ansible against some Vagrant VMs. It will also show you how to write Ansible in such a way that mulitple OSes can be targeted from one playbook/role. The Vagrant VMs set up here will allow you to target multiple OSes at once.

## Example Ansible Pre-requirements

### Install Ansible

Please install the latest version of Ansible. Optional: Install an older version of Ansible (E.g. 1.9.3).

To do so, follow the instructions found in this wiki, which can be found [here.](https://github.com/nfaction/ansible-tips-and-tricks/wiki/Installing-Ansible)

### Install Vagrant

Please take a moment to install Virtualbox, if you have not already done so.

General instructions can be found here:

* Virtualbox: <https://www.virtualbox.org/wiki/Linux_Downloads>
* Vagrant: <https://www.vagrantup.com/downloads.html>

#### Mac

Install Virtualbox

```
brew install Caskroom/cask/virtualbox
```

Install Vagrant

```
brew install Caskroom/cask/vagrant
```

#### Linux

Below is a quick example of an older Debian based OS install

Install Virtualbox: <https://www.virtualbox.org/wiki/Linux_Downloads>

Install Vagrant

```
sudo apt-get install vagrant
```


## Run the Ansible Examples

From this directory, follow the instructions to install Ansible with Python Pip, then activate the environment prior running code below. This assumes that you have the latest version of Ansible installed and Virtualbox + Vagrant installed.

### Get the code

Clone this repository and activate your Ansible environment:
    
```
# Clone this repository
git clone https://github.com/nfaction/ansible-tips-and-tricks.git
    
cd ansible-tips-and-tricks/
    
source ~/ansible-2/bin/activate
```


### Launch your test environment

If you have not already generated your ssh private/public keys, do that now. If you don't know how to do that, follow these [steps.](https://github.com/nfaction/ansible-tips-and-tricks/tree/master/examples/vagrant#ssh-publicprivate-keys)

Start up your Vagrant test instance

```
# Navigate to the Vagrant environment
cd examples/vagrant/
	
# Start up your first test VM (Be sure that you have already activated your Ansible VENV)
vagrant up
	
# Be patient, the "up" operation may take a few minutes
	
# When your prompt returns, go back to the Ansible directory
cd ..
```

See if Ansible can `ping` your newly launched VM

```
# Ping the vagrant vm
ansible single-box -m ping
    
# You should see something like this:
(ansible-2) :examples $ ansible single-box -m ping
vagrant-box | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

If you got a `"pong"` proceed to the next section.

If the last command failed to ping and you see something like this:

```
vagrant-box | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 127.0.0.1 port 2222: Connection refused\r\n",
    "unreachable": true
}
```

Please verify that your VM is running and Ansible is activated. You can use these commands to test that:

```
# Make sure your vagrant VM is running
$ cd vagrant/ && vagrant status && cd ..
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`

# Make sure Ansible is activated
$ ansible --version | grep 'ansible\ 2'
ansible 2.6.2
```

## Run your first Ansible Playbook

### Run SSH Example Playbook

Run the `ssh.yml` playbook to see how Ansible configures SSH

```
$ ansible-playbook playbooks/ssh.yml --limit single-box
```

You should see something like this:

```
(ansible-2) :examples $ ansible-playbook playbooks/ssh.yml --limit single-box

PLAY [set up and configure SSH] ****************************************************************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box]

TASK [ssh : gather os specific variables] ******************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box] => (item=/Users/matt0380/demo/ansible-tips-and-tricks/examples/roles/ssh/vars/Ubuntu.yml)

TASK [ssh : install dependencies] **************************************************************************************************************************************************************************************************************************************************************************************************

TASK [ssh : install ssh server] ****************************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box]

TASK [ssh : copy motd.ssh] *********************************************************************************************************************************************************************************************************************************************************************************************************
changed: [vagrant-box]

TASK [ssh : configure ssh] *********************************************************************************************************************************************************************************************************************************************************************************************************
changed: [vagrant-box] => (item=/Users/matt0380/demo/ansible-tips-and-tricks/examples/roles/ssh/templates/Ubuntu-14.sshd_config.j2)

TASK [ssh : start ssh service] *****************************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box]

TASK [ssh : make sure ssh directory exists] ****************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box]

TASK [ssh : add ssh keys to authorized_keys] ***************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box] => (item=ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCrX6ctNQ+1sHmLvFZLEZrwncLmng6w1AbJP88ZDZu9AiKU5UOdn43ocHCL9+3l3bIL5Or5BBv76T0VTFEcYwsnJcpxP8lld7GWY+AjewpzRmGKhT/AG2I4Q4nZObM8fZU5JFZqsETqhNx3r+HRjIESNFVZLlJrSn1I7tPI89iKvDKTxYLgpurgHDvtCtq003dfHjmXiRwaPHBII9JY+WOtXY8pqMm1uddmWAK6aa7F4rsdps9yCyGHZCOjlD4JNzflL7RDD7v1DT1vgygNeISVevk9idTu9UzVVHw0tkVjfpqiahHiJPCIQ0KDZO9ZujLj7VJNXeLF8j7D/tqai0ex key-to-use)
changed: [vagrant-box] => (item=ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/J4BW3hmU0DEtmw0D6JvIF7SNuYxIk4pgWwpKbaos5eaIh0V3RxjyiRQrMPI6Ib4wM5jqeiwschMORnqWZoIkXSHRSsjjzq47AfxfhrFevB2twCuq25bKK4IZSXiJV+Wfyeh1L0ztd9owRI2adz7fF5p8vGx0KitYLXJ8pgnSW1wDxrnr1Q96lFV9oPyzt4/RiosRvfkNI0h2MqLeATBB4tqByDTIcJpeF4u23NUtOqG1iRElZ8/6ynlPw+09FgRYGnphpgNOpguOlKcg/9zHZCGVZoayaHw7b/6Pj38u7ofOTrZFnEiVO4qNhBilazgxJ9G7DLEJJ3QNiDYQ8SLb key-to-add)
changed: [vagrant-box] => (item=ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDi5bk8lyzmZXYUZOTph7VBR5CC97IGsCp12M3+1ZHKDsH59RpLSxBdfP/93N4XC7Xws8oeqckoRZWVCaNHH1dpBGLPm0WKscRVqk4SCGBENer+s2KpIP0HqJfL8SLpxfLBFrMXWkq63oFOEnjjqX3eKQKQlYUdAQpAHj/vpSPq7G14IXTIsxC167ycVO5YqtBX8TCCBRaEC7cLMstQxfDWFR/XbAqh3AjS7N1rrXlRArGoY6gMQu3vDc652DjqiyQdiK6EEVovBWQSsL6TC7i7QKeLpN3CVmNyfDMuWkks29FvRNTG+TC4ZzAhOWWPM2eAlXBflQv9HTCmTT9G9qgb key-to-remove)

TASK [ssh : remove ssh keys from authorized_keys] **********************************************************************************************************************************************************************************************************************************************************************************
changed: [vagrant-box] => (item=ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDi5bk8lyzmZXYUZOTph7VBR5CC97IGsCp12M3+1ZHKDsH59RpLSxBdfP/93N4XC7Xws8oeqckoRZWVCaNHH1dpBGLPm0WKscRVqk4SCGBENer+s2KpIP0HqJfL8SLpxfLBFrMXWkq63oFOEnjjqX3eKQKQlYUdAQpAHj/vpSPq7G14IXTIsxC167ycVO5YqtBX8TCCBRaEC7cLMstQxfDWFR/XbAqh3AjS7N1rrXlRArGoY6gMQu3vDc652DjqiyQdiK6EEVovBWQSsL6TC7i7QKeLpN3CVmNyfDMuWkks29FvRNTG+TC4ZzAhOWWPM2eAlXBflQv9HTCmTT9G9qgb key-to-remove)

TASK [ssh : disable selinux] *******************************************************************************************************************************************************************************************************************************************************************************************************
skipping: [vagrant-box]

TASK [ssh : add ssh port to selinux] ***********************************************************************************************************************************************************************************************************************************************************************************************
skipping: [vagrant-box]

TASK [ssh : remove old ssh port to selinux (if applicable)] ************************************************************************************************************************************************************************************************************************************************************************
skipping: [vagrant-box]

TASK [ssh : restart ssh service] ***************************************************************************************************************************************************************************************************************************************************************************************************
changed: [vagrant-box]

PLAY RECAP *************************************************************************************************************************************************************************************************************************************************************************************************************************
vagrant-box                : ok=10   changed=5    unreachable=0    failed=0
```

Congratulations! You have a working test environment to run Ansible!

## Run NTP Example Playbook

Now let's try to configure NTP:

```
$ ansible-playbook playbooks/ntp.yml --limit single-box
```

You should see something like this:

```
(ansible-2) :examples $ ansible-playbook playbooks/ntp.yml --limit single-box

PLAY [install and configure NTP] ***************************************************************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box]

TASK [ntp : gather os specific variables] ******************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box] => (item=/Users/matt0380/demo/ansible-tips-and-tricks/examples/roles/ntp/vars/Ubuntu.yml)

TASK [ntp : install ntp package] ***************************************************************************************************************************************************************************************************************************************************************************************************
changed: [vagrant-box]

TASK [ntp : verify that ntp is installed] ******************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box]

TASK [ntp : update system date manually] *******************************************************************************************************************************************************************************************************************************************************************************************
skipping: [vagrant-box]

TASK [ntp : update hardware clock manually] ****************************************************************************************************************************************************************************************************************************************************************************************
skipping: [vagrant-box]

TASK [ntp : configure ntp] *********************************************************************************************************************************************************************************************************************************************************************************************************
changed: [vagrant-box] => (item=/Users/matt0380/demo/ansible-tips-and-tricks/examples/roles/ntp/templates/Ubuntu.ntp.conf.j2)

TASK [ntp : start ntp service] *****************************************************************************************************************************************************************************************************************************************************************************************************
ok: [vagrant-box]

PLAY RECAP *************************************************************************************************************************************************************************************************************************************************************************************************************************
vagrant-box                : ok=6    changed=2    unreachable=0    failed=0
```

Let's see what just happened.

Verify that NTP is actually running and installed:

```
$ ansible single-box -m shell -a "date && ntpq -p"
```

You should see something similar to this:

```
(ansible-2) :examples $ ansible single-box -m shell -a "date && ntpq -p"
vagrant-box | SUCCESS | rc=0 >>
DOW MON  DOM HH:MM:SS UTC YYYY
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 NTP1            .INIT.          16 u    -   64    0    0.000    0.000   0.000
 NTP2            .INIT.          16 u    -   64    0    0.000    0.000   0.000
 NTP3            .INIT.          16 u    -   64    0    0.000    0.000   0.000
 NTP4            .INIT.          16 u    -   64    0    0.000    0.000   0.000
 NTP5            .INIT.          16 u    -   64    0    0.000    0.000   0.000
```

If you are done and want to tear down your VM, go ahead and destroy your `vagrant` box
 
```
# vagrant destroy your instance (Will delete and remove the VM from Virtualbox)
$ cd vagrant/ && vagrant destroy -f && cd ..
```

## Now with multiple OSes

### Launch your test environment

This will be very similar to what you just did for `single-box`, this time however you will be launching/running 2 instances (Ubuntu and CentOS) and deploying to them at the same time, instead of just one instance.

```
# Navigate to the Vagrant environment
cd vagrant/multi_os_vagrant/
	
# Start up both test VMs (Be sure that you have already activated your Ansible VENV)
vagrant up
	
# Be patient, the "up" operation may take a few minutes.
# This time you should see the "up" operation happen twice.
	
# When your prompt returns, go back to the Ansible directory
cd ../../
```

Ansible `ping` your newly launched VMs

```
# Ping the vagrant vm
ansible multi-os -m ping
```

You should see something like this:

```
(ansible-2) :examples $ ansible multi-os -m ping
ubuntu | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
centos | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

### Run the Example Playbooks again

Just as before, run the `ssh.yml` playbook, but notice that this time you will see two hosts being deployed at the same time.

```
$ ansible-playbook playbooks/ssh.yml --limit multi-os
```

Now run the `ntp.yml` playbook:

```
$ ansible-playbook playbooks/ntp.yml --limit multi-os
```

This time, check both boxes for proper NTP configuration:

```
$ ansible multi-os -m shell -a "date && ntpq -p"
```

This time, you will see two hosts output instead of one.

Thats it!

## Unpacking the code

Now let's do a deep dive into these playbooks and roles.

### SSH Role

Ansible SSH Playbook: [ssh.yml](https://github.com/nfaction/ansible-tips-and-tricks/blob/master/examples/playbooks/ssh.yml)

Ansible SSH Role: [ssh](https://github.com/nfaction/ansible-tips-and-tricks/tree/master/examples/roles/ssh)

### NTP Role

Ansible NTP Playbook: [ssh.yml](https://github.com/nfaction/ansible-tips-and-tricks/blob/master/examples/playbooks/ntp.yml)

Ansible NTP Role: [ssh](https://github.com/nfaction/ansible-tips-and-tricks/tree/master/examples/roles/ntp)
