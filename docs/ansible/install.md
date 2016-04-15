The instructions provided here is used to setup Ansible for managing other systems. A unique characteristic of Ansible is that is does not require a centralized management model. Although the instructions are general, the dependency names are specific to Ubuntu.

# Installation on the Ansible server

## Using pip
```
pip install --upgrade pip virtualenv virtualenvwrapper
virtualenv ansible2.0
source ansible2.0/bin/activate
pip install ansible
pip install redis
```

## Using Git

```
git clone git://github.com/ansible/ansible.git
```

Switch to version 1.9

```
git checkout stable-1.9
source ansible/hacking/env-setup
```
