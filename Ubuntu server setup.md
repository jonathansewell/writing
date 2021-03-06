# Ubuntu Cloud Server Setup

## Users and SSH Setup

**login**

ssh root@123.123.123.123

**install updates**

apt-get update && apt-get upgrade

**configure automatic updates**

https://help.ubuntu.com/lts/serverguide/automatic-updates.html

**create a sudo user**

```bash
adduser example_user
adduser example_user sudo
```

**install ssh keys**

On the server, as your new user:

```bash
mkdir ~/.ssh
chmod 700 ~/.ssh
```

On your Mac:

`scp ~/.ssh/id_rsa.pub example_user@203.0.113.0:~/.ssh/authorized_keys`

You should be able to `ssh` to this machine now, if you get prompted for the password for example_user you've probably not got teh correct permissions or ownership on the .ssh folder.

**disable root login**

`sudo vim /etc/ssh/sshd_config`

Update to:
>PermitRootLogin no

## Github
Set up port forwarding https://developer.github.com/guides/using-ssh-agent-forwarding/

## Node

Install NVM via curl, find the command here: https://github.com/creationix/nvm

Remember to reload your bash profile before you can use `nvm`.

## Port forwarding
(http://stackoverflow.com/questions/16573668/best-practices-when-running-node-js-with-port-80-ubuntu-linode)

Add to /etc/rc.local:

`iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 3001`

This applies the forwarding after a reboot.

To apply it now run the same command with sudo.
