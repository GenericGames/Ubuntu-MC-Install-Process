# Generic's Install Process for ubuntu Server

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

## Pterodactyl Panel install

This is only done on one machine.
If the panel is already installed move onto Pterodactyl Wings install.
[Link to documentation](https://pterodactyl.io/project/introduction.html)

### Dependencies

Switch to root

```sh
sudo -s
#Will need to be in root for multiple of the below commands.
```

Add "add-apt-repository" command

```sh
apt -y install software-properties-common curl apt-transport-https ca-certificates gnupg
```

Add additional repositories for PHP, Redis, and MariaDB

```sh
LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php
add-apt-repository -y ppa:chris-lea/redis-server
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
```

Update repositories list

```sh
apt update
```

Add universe repository if you are on Ubuntu 18.04

```sh
apt-add-repository universe
```

Install Dependencies

```sh
apt -y install php7.4 php7.4-{cli,gd,mysql,pdo,mbstring,tokenizer,bcmath,xml,fpm,curl,zip} mariadb-server nginx tar unzip git redis-server
```

Install Composer

```sh
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

Create directory

```sh
mkdir -p /var/www/pterodactyl
cd /var/www/pterodactyl
```

Download files

```sh
curl -Lo panel.tar.gz https://github.com/pterodactyl/panel/releases/download/v1.1.1/panel.tar.gz
tar -xzvf panel.tar.gz
chmod -R 755 storage/* bootstrap/cache/
```

### Setting up MySQL

Log In

```sh
mysql -u root -p
```

Set to use mysql

```sql
USE mysql;
#Note - If you have to exit at any point after this make sure to use this command again
```

Create user

```sql
# Remember to change 'somePassword' below to be a unique password specific to this account.
CREATE USER pterodactyl@127.0.0.1 IDENTIFIED BY 'somePassword';
```

Create database

```sql
CREATE DATABASE panel;
```

Assign permissions

```sql
GRANT ALL PRIVILEGES ON panel.* TO 'pterodactyl'@'127.0.0.1' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

Leave MariaDB/MySQL

```sh
exit
```

### Installation

cd back into the directory we created above

```sh
cd /var/www/pterodactyl
```

Copy default default environment setting file, install core dependencies, and general all application encryption key

```sh
cp .env.example .env
composer install --no-dev --optimize-autoloader
#The above command will tell you to not run it in root, it will not run in this case if it's not ran in root.
```

Only run the command below if you are installing this Panel for the first time and do not have any Pterodactyl Panel data in the database

```sh
php artisan key:generate --force
```

### Environment Configuration
