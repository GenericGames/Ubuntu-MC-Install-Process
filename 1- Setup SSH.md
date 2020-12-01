# SSH setup

[Back to README](README.md)

## Lock root

`sudo passwd --lock root`

## Set up SSH, shortcut, key, disable password login

Update the system

```sh
sudo apt update
sudo apt upgrade -y
```

Install openssh-server

```sh
sudo apt install openssh-server -y
```

Verify that ssh service running

```sh
sudo systemctl status ssh
```

If not running

```sh
sudo systemctl enable --now ssh
```

Configure firewall and open port 22

```sh
sudo ufw allow ssh
sudo ufw enable
sudo ufw status
```

To get IP

```sh
ip a
```

Test login with SSH

```sh
ssh user@"server-ip"
```

Create shortcut for ssh login on local machine

```sh
cd ~/.ssh
nano config

#add below to file

Host "hostname"
    User "username"
    Hostname "IP"
    Port "Port"
```

Test if shortcut works

```sh
ssh "hostname"
```

Set up login with key

```sh
ssh-copy-id "hostname" #do this on local pc
```

Test if ssh-copy-id worked

```sh
ssh "hostname"
```

disable login with password

```sh
sudo nano /etc/ssh/sshd_config

#change from

"#PasswordAuthentication yes"

# to - Make sure to remove the # at the start

"PasswordAuthentication yes"
```

restart ssh

```sh
systemctl restart ssh
```

test if password works

```sh
ssh -o PreferredAuthentications=password "hostname"
```

If the Pterodactyl Panel has not already been installed on a machine than continue to the panel install
[Pterodactyl Panel install](2%20-%20Pterodactyl%20Panel%20install.md)

If it has already been installed than skip and go to the wings install
[Pterodactyl Wings install](3%20-%20Pterodactyl%20Wings%20install.md)

[Back to README](README.md)
