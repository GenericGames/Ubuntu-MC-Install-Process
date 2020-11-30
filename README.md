# Generic's Install Process for ubuntu Server

## Lock root

`sudo passwd --lock root`

## Set up SSH, shortcut, key, disable password login

Update the system

```bash
sudo apt update
sudo apt upgrade #when asked press "y"
```

Install openssh-server

```bash
sudo apt install openssh-server #When asked press "y"
```

Verify that ssh service running

```bash
sudo systemctl status ssh
```

If not running

```bash
sudo systemctl enable --now ssh
```

Configure firewall and open port 22

```bash
sudo ufw allow ssh
sudo ufw enable
sudo ufw status
```

To get IP

```bash
ip a
```

Test login with SSH

```bash
ssh user@"server-ip"
```

Create shortcut for ssh login on local machine

```bash
cd ~/.ssh
nano config

#add below to file

Host "hostname"
    User "username"
    Hostname "IP"
    Port "Port"
```

Test if shortcut works

```bash
ssh "hostname"
```

Set up login with key

```bash
ssh-copy-id "hostname" #do this on local pc
```

Test if ssh-copy-id worked

```bash
ssh "hostname"
```

disable login with password

```bash
sudo nano /etc/ssh/sshd_config

#change from

"#PasswordAuthentication yes"

# to - Make sure to remove the # at the start

"PasswordAuthentication yes"
```

restart ssh

```bash
systemctl restart ssh
```

test if password works

```bash
ssh -o PreferredAuthentications=password "hostname"
```

## Pterodactyl Panel install

This is only done on one machine.
If the panel is already installed move onto Pterodactyl Wings install.
