# Adding your public key via password access

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

# Adding your public key when you don't have direct access

If the server is configured to only public-key authed connections via SSH (and does not permit password access) then things are a little more tricky, but still doable via an already trusted device.

On the device that you want to add, run

```zsh
cat ~/.ssh/id_rsa.pub
```

This will show you the public key that you need to add to the server's `~/.ssh/authorized_keys`. You can do this by logging into the server using the trusted machine.

There are a few options to get that key across:

- `scp` the file from the untrusted machine to a staging location on the trusted machine, if you can log into that from the machine not trusted by the intended server.
- Copy the text of the key and `ssh` into the middle machine. Then `ssh` again from that machine into the server. Your clipboard can be used to paste the key in the next step.
- Follow the above `ssh-copy-id` steps to add the key to the middle machine's list of authorised keys. Then you can physically find that key on the middle machine in the list of authed keys and then use `ssh` to put that on the final machine.

These are very unclear descriptions but the idea is that you're going to log into the server using/via the middle machine, and then use something like the following command to append the previously-untrusted machine's public key to the server's list of trusted public keys.

```zsh
echo 'paste key here' >> ~/.ssh/authorized_keys
cat temp_id_rsa.pub >> ~/.ssh/authorized_keys
```

**Do not forget the double `>>`** (which means "append to file") — using a single `>` will overwrite the file and cause more grief as all of your previously trusted machines are wiped.

Observe that the `authorized_keys` file is just a list of trusted public keys, one per line, with the keys reproduced exactly as they are represented in their represented machine's `id_rsa.pub` file.

# Remote access via ngrok.io

I have created an ngrok.io account using `uoa.p4p.83.2024@gmail.com`. Credentials are on Discord (in the reference channel).

The instructions to get started are shown on our Ngrok dashboard.

You can make a Pi accessible remotely using `ngrok tcp 22` (22 being the designated `ssh` port).

You can connect to a remotely accessible Pi using `ssh -p 18075 james@0.tcp.au.ngrok.io`, where in this example `18075` is the port, `james` is the user, and `0.tcp.au.ngrok.io` is the forwarding server. (This information will be displayed on the Pi in the terminal running the above `ngrok tcp localhost:22` command, but if you don't have physical access you can also find this through the [Agents dashboard](<https://dashboard.ngrok.com/tunnels/agents>) online.)

Ngrok also allows you to get a static domain, but unfortunately this only works for `https` (not `tcp`) and `ssh` doesn't seem to work over `https` (only `tcp`).

# Enabling or disabling password access

> [!info] Merge conflict
> James — you can add your changes re. password access in here if you wish. Sorry for the hassle, but I did want to jot the above down.