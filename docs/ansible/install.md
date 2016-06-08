The instructions provided here is used to setup Ansible for managing other systems. A unique characteristic of Ansible is that is does not require a centralized management model. Although the instructions are general, the dependency names are specific to Ubuntu.

# Installation on the Ansible server

## Using pip

### Ansible 2.1
```
pip install --upgrade pip virtualenv virtualenvwrapper
virtualenv ansible2.1
source ansible2.1/bin/activate
pip install ansible==2.1
pip install redis
```

### Ansible 2.0
```
pip install --upgrade pip virtualenv virtualenvwrapper
virtualenv ansible2.0
source ansible2.0/bin/activate
pip install ansible==2.0.1.0
pip install redis
```

### Ansible 1.9
```
pip install --upgrade pip virtualenv virtualenvwrapper
virtualenv ansible1.9
source ansible1.9/bin/activate
pip install ansible==1.9.4
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

# Installation on a Mac

One could just simply `brew` install Ansible, but it does not always set up the system to run Ansible correctly.  If while running Ansible an error is displayed regarding logs or permissions, run the following:

```
sudo mkdir -p /var/log/ansible
sudo touch /var/log/ansible/ansible.log
sudo chown -R ${USER}:wheel /var/log/ansible
sudo chmod 775 /var/log/ansible
sudo chmod 774 /var/log/ansible/ansible.log
```

# Installing on Ubuntu

Simply install using `apt` then run Python VirtualEnv

```
sudo apt-get install ansible
```

Then follow the procedure in the first section of this document

# Troubleshooting SSH Connections

Sometimes when attempting an initial Ansible `ping`, it can fail in a very confusing way.  If this happens, try adding something like this to your `~/.ssh/config` file:

```
Host <hostname-prefix>*
  Port 22
  User <ssh-login-username>
  StrictHostKeyChecking=no
  UserKnownHostsFile=/dev/null
```

Where anything between `< >` needs to be changed.
