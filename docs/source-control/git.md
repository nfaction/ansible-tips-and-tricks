It is often a very good idea to test code before it is commited anywhere for others to use.  It often helps to check for syntax errors using `--syntax-check` as well as running the playbook against the actual environment the code is written for and verifying output.  

To ensure that no bad code ever gets commited, it is a good idea to configure your repository to first check for errors.

# Checking for Syntax Errors
Checking all code for errors in syntax

```
ansible-playbook playbooks/test.yml --syntax-check
```

# Git Hook

Add a line with the respective paths to your .git/hooks/pre-commit file to have ansible check your code before committing:

```

#!/bin/sh
 
<ansible_base_dir>/ansible-playbook --check --list-tasks -i <ansible_base_dir>/playbooks/*.yml
 
if [ $? -ne 0 ]
then
    exit 1
fi
```