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
    - apache2
```

# Adding/Removing Repositories

Sometimes it is required that you first configure a non-standard repository before one can install packages.  These modules do this:

## Ubuntu

<http://docs.ansible.com/ansible/apt_key_module.html>
<http://docs.ansible.com/ansible/apt_repository_module.html>

## CentOS
Be aware that this is only available via Ansible 2.1

<http://docs.ansible.com/ansible/yum_repository_module.html>