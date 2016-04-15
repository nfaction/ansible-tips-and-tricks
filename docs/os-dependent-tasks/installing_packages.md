Ansible uses separate modules depending on OS, which means that writing a play that can install to multiple OSes requires lots of `when:` to check OS before install.  Instead you can use gathered facts to automatically decide.

Those modules can be found here:
Ubuntu: <http://docs.ansible.com/ansible/apt_module.html>
CentOS: <http://docs.ansible.com/ansible/yum_module.html>

# Installing packages

This method requires that `fact_gathering` is enabled.

Use the `action` module to variableize actual module calls

```
- name: install basic package
  action: >
    {{ ansible_pkg_mgr }} name=vim state=present update_cache=yes
```

You can use Ansible's pseudo looping method to install multiple packages

```
- name: install basic packages
  action: >
    {{ ansible_pkg_mgr }} name={{ item }} state=present update_cache=yes
  with_items:
    - vim
    - tmux
    - mosh
```

# Installing System Updates
Please note that the modules for `apt` and `yum` have different options that make it impossible to use the method above of using the `action:`  In order to do this, one must use the `when:` to perform their associated module per OS.

`yum` does not have a update module option, so it basically check to see if all packages are up to date.  `apt` upgrades to dist.

```
- name: install system updates for centos systems
  yum: name=* state=latest update_cache=yes
  when: ansible_distribution == "CentOS"

- name: install system updates for ubuntu systems
  apt: upgrade=dist update_cache=yes
  when: ansible_distribution == "Ubuntu"
```

# Adding/Removing Repositories

Sometimes it is required that you first configure a non-standard repository before one can install packages.  These modules do this:

## Ubuntu

<http://docs.ansible.com/ansible/apt_key_module.html>
<http://docs.ansible.com/ansible/apt_repository_module.html>

```
- name: add gnupg key for codership repo
  apt-key: keyserver=keyserver.ubuntu.com id=BC19DDBA
  
- name: add repo
  apt_repository: repo='deb http://ubuntu-cloud.archive.canonical.com/{{ ansible_distribution | lower }} {{ ansible_distribution_release }}-updates/liberty main' state=present

```

## CentOS
Be aware that this is only available via Ansible 2.1

<http://docs.ansible.com/ansible/yum_repository_module.html>

# Manually Adding Source List
In some cases extra arguments are required to add a repository.  If this is the case, sometimes it is best just to use the `shell` module and run the command manually.  In other cases, it requires that a source list be built, which is usually best done via `template` modules.  See below.

```
- name: enable codership repo in yum
  template: src=galera.repo.j2 dest=/etc/yum.repos.d/galera.repo
```

Template:

```
[galera]
name = Galera
baseurl = http://releases.galeracluster.com/{{ ansible_distribution | lower }}/{{ ansible_distribution_major_version }}/{{ ansible_architecture }}
gpgkey = http://releases.galeracluster.com/GPG-KEY-galeracluster.com
gpgcheck = 1
```