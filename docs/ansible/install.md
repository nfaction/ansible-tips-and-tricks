The instructions provided here is used to setup Ansible for managing other systems. A unique characteristic of Ansible is that is does not require a centralized management model. Although the instructions are general, the dependency names are specific to Ubuntu.

# Installation on the Ansible server

```
git clone git://github.com/ansible/ansible.git
```

Switch to version 1.9

```
git checkout stable-1.9
source ansible/hacking/env-setup
```
