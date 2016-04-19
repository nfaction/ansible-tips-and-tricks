# Configure SSH using Ansible

## Use standard SSH port

Using port 22 on the host, with port forward to `localhost` at 2222

```
ansible-playbook playbooks/ssh.yml -i hosts
```

At this point one can login like this:

```
ssh -p 2222 -i vagrant/ssh_keys/key-to-use root@127.0.0.1
```

## Use alternate SSH port

This will actually move the `SSH` port to 2222, making login with the command above impossible

```
ansible-playbook playbooks/ssh.yml -i hosts -e "SSH_PORT=2222"
```

At this point one can login like this:

```
ssh -p 2222 -i vagrant/ssh_keys/key-to-use root@192.168.100.10
```
