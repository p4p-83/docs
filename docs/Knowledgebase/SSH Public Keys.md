Setting up access via SSH public keys can be done entirely on the client machine. Do the following. Derived from <https://www.ssh.com/academy/ssh/copy-id>.

```zsh

# make sure your host machine has a key pair
ls ~/.ssh

# if there is no id_<algorithm> pair
ssh-keygen

# now tell the target that this is your identity
ssh-copy-id -i ~/.ssh/id_rsa Sam@rpi5.local

```

And you're done.