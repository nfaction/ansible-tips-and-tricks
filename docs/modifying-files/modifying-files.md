# Using Ansible to modify files
Managing many configuration files can be tedious.  There may be vast differences between OS and service.  This page is intended to show how to modify configuration and other files on systems

## Ansible template module

Ansible Templates are very useful for configuring a system with lots of changes per configuration file.  They can also be helpful when trying to build out vastly different configurations automatically by using dynamic/changing variables.

Documentation for them can be found [here.](http://docs.ansible.com/ansible/template_module.html)

To properly leverage templates, it requires that one is already using `with_first_found` function within the `include_vars` module for OS separation.  For more information on this, see the docs [here.](../os-dependent-tasks/variables.md)

A decent example of how templates work, can be found in the examples within this repository.  Here is the snipit for how to do this:

```
- name: gather os specific variables
  include_vars: "{{ item }}"
  with_first_found:
    - "{{ ansible_distribution }}-{{ ansible_distribution_major_version}}.yml"
    - "{{ ansible_distribution }}.yml"
    - "defaults.yml"
  tags: vars

- name: configure ssh
  template: src={{ item }} dest={{ SSH_CONFIG }} backup=yes
  with_first_found:
    - "{{ ansible_distribution }}-{{ ansible_distribution_major_version }}.sshd_config.j2"
    - "{{ ansible_distribution }}.sshd_config.j2"
  tags: template-sshd-config
```

The directory structure will look something like this:

```
ssh/
...
├── tasks
│   └── main.yml
├── templates
│   ├── CentOS-5.sshd_config.j2
│   ├── CentOS-6.sshd_config.j2
│   ├── CentOS-7.sshd_config.j2
│   ├── Ubuntu-12.sshd_config.j2
│   └── Ubuntu-14.sshd_config.j2
└── vars
    ├── CentOS.yml
    └── Ubuntu.yml
```

Vagrant can be extremely useful in getting blank configuration files to then be used as templates later on.  One important thing to note is that configuration files change, so be sure to replace templates with updated versions whenever new ones are released.  CentOS system configurations will be appended with an `*.rpm.new` where as Ubuntu systems will append a `*.dpkg-old` OR `*.dpkg-dist`

## Ansible lineinfile module

`lineinefile` + `with_items`

```
- name: add groups to sudoers
  lineinfile: dest=/etc/sudoers regexp="^root(\s+)ALL=(ALL)(\s+)ALL" insertafter="^root" line='{{ item }}' state=present backup=yes backrefs=yes
  with_items:
    - '%admin\tALL=(ALL:ALL)\tALL'
    - '%users\tALL=(ALL:ALL)\tALL'
  tags: sudoers
```

Using lineinfile to comment out a line:

```
ansible -m lineinfile -i hosts -e "ansible_ssh_port=2222" -a "dest=/etc/aliases regexp='^(.*)root:(.*)' line='#root:           systems@domain.com'" host-group --limit "host1"
```

```
BEFORE:
root:           systems@domain.com
 
AFTER:
#root:           systems@domain.com
```

## Ansible ini_file module

This module is a life-saver when modifying `ini` type files with many changes.

The configuration file may look like this:

```
[DEFAULT]
# Print more verbose output (set logging level to INFO instead of default WARNING level).
# verbose = False
# Print debugging output (set logging level to DEBUG instead of default WARNING level).
# debug = False
```

ansible multi-os -m ini_file -a "dest=/etc/setting.conf section=DEFAULT option=verbose value=True backup=yes" -i hosts

`ini_file` module example:

```
- name: enable verbose mode
  ini_file: dest=/etc/setting.conf section=DEFAULT option=verbose value=True backup=yes
  tags: configuration
```

After change:

```
[DEFAULT]
# Print more verbose output (set logging level to INFO instead of default WARNING level).
verbose = True
# Print debugging output (set logging level to DEBUG instead of default WARNING level).
# debug = False
```
