When working with multiple OSes, inevitably there will be packages, configuration files and service calls that are vastly different than one another.  There can even be instances in which sub-versions of OSes can have major differences, such as `service` vs. `systemctl` in CentOS 6 vs. 7

Defined here are different ways to handle these issues both in the same `playbook` or `play` or by separating out tasks to other task files.

# Task separation via Variables

This method pulls in variables based on the client's OS and uses those variables specific to that machine's needs.

A solution to this problem uses the Ansible module `include_vars` with the looping mechanism `with_first_found`

``` yaml
- name: gather os specific variables
  include_vars: "{{ item }}"
  with_first_found:
    - "{{ ansible_distribution }}-{{ ansible_distribution_major_version}}.yml"
    - "{{ ansible_distribution }}.yml"
  tags: vars
```

# Task separation via Includes (Deprecated in 2.0)

Alternatively, one could separate tasks into a set of tasks that are OS specific, then come back and finish execution of the `playbook`

``` yaml
- include: centos5-setup.yml
  when: ansible_distribution == "CentOS" and ansible_distribution_major_version < '6'
- include: centos6-setup.yml
  when: ansible_distribution == "CentOS" and ansible_distribution_major_version == '6'
- include: centos7-setup.yml
  when: ansible_distribution == "CentOS" and ansible_distribution_major_version > '6'
- include: ubuntu-setup.yml
  when: ansible_distribution == "Ubuntu"
 
OR
 
# Include OS specific tasks
- include: CentOS.yml
  when: ansible_distribution == "CentOS"
- include: Ubuntu.yml
  when: ansible_distribution == "Ubuntu"
```
