# Running Ansible Manually

## Running Modules Manually

* Ping hosts
  
  ```
  ansible -m ping newservers
  ```

* Display gathered facts
  
  ```
  ansible -m setup newservers | less
  ```

* Filter gathered facts
  
  ```
  ansible newservers -m setup -a "filter=ansible_distribution*"
  ```
  

