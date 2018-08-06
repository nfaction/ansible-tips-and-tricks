# SSH Public/Private Keys

SSH private/public keypairs are used to login to a system with or without a passphrase.  This is vastly more secure than logging in with a password.  This also means that the private key should NEVER be shared with anyone.

## Generating SSH Keys

The following command will generate two files, a `<your_key_file_name>` and `<your_key_file_name>.pub` in the `~/.ssh/` directory.

```
$ ssh-keygen -N '' -t rsa -b 2048 -f ~/.ssh/<your_key_file_name> -C <your_key_name>
```
