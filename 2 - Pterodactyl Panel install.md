# Pterodactyl Panel Install

[Back to README](README.md)

## Note

This is only done on one machine.
If the panel is already installed move onto [Pterodactyl Wings install](3%20-%20Pterodactyl%20Wings%20install.md).

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

### Setup MySQL

Before continuing to the next step MySQL needs to be partially set up.

[MySQL setup](Setting%20up%20MySQL.md#creating-a-database-for-pterodactyl)

### Installation

cd back into the directory we created above

```sh
cd /var/www/pterodactyl
```

Copy default default environment setting file, install core dependencies, and general all application encryption key

```sh
cp .env.example .env
composer install --no-dev --optimize-autoloader
#The above command will tell you to not run it in root, ignore it.
#It will not work in this case if it's not ran in root.
```

Only run the command below if you are installing this Panel for the first time and do not have any Pterodactyl Panel data in the database

```sh
php artisan key:generate --force
```

### Environment Configuration

#### Setup

```sh
php artisan p:environment:setup
#Note Everything in Brackets [] is default setting, press enter if you want default
```

```sh
Egg Author Email [unknown@unknown.com]
#For now use default (hit enter) when setting up on dedicated server type in your email
```

```sh
Application url [http://localhost]
#For now use default (hit enter) when setting up on dedicated server use https://simplegaming.gg/
```

```sh
Application timezone [America/New_York]:
#America/Phoenix
```

```sh
Cache Driver [Filesystem]:
Redis
```

```sh
Queue Driver [MySQL Database]:
Default (Hit enter)
```

```sh
Enable UI based settings editor (yes/no) [yes]:
#Default (hit enter)
```

```sh
Redis Host [localhost]:
Default (hit enter)
```

```sh
Redis Password:
#default (hit enter)
```

```sh
Redis Port [6379]:
#Default (hit enter)
```

#### Database

```sh
php artisan p:environment:database
#Note Everything in Brackets [] is default setting, press enter if you want default
```

```sh
Database Host [127.0.0.1]:
#Default (hit enter)
```

```sh
Database Port [3306]:
Default (hit enter)
```

```sh
Database Name [panel]:
#default (hit enter) unless panel name was changed earlier in the configuration
```

```sh
Database Username [pterodactyl]:
#Default (hit enter) unless database username was changed earlier in the configuration
```

```sh
Database Password:
#Default is set to "somePassword" for testing purposes but should have been changed for actual install
```

#### Mail

```sh
php artisan p:environment:mail
#Note Everything in Brackets [] is default setting, press enter if you want default
```

```sh
Which driver should be used for sending emails [SMTP Server]:
Mail
```

```sh
Email address emails should originate from [no-reply@example.com]
#for now use default (hit enter)
```

```sh
Name that emails should appear from [Pterodactyl Panel]:
#Default (hit enter)
```

```sh
Encryption method to use [TLS]:
None
```

### Database Setup

```sh
php artisan migrate --seed --force
```

### Database User Setup

Add the first user

```sh
php artisan p:user:make
```

```sh
Is this user an administrator (yes/no) [no]:
yes
```

```sh
Email Address:
#Put in email address
```

```sh
Username:
#Put in username
```

```sh
First Name:
#Put in first name
```

```sh
Last Name:
#put in last name
```

```sh
Password:
#For testing we will use "Password1" when dedicated is set up we will use actual password. Enter password - Note: Must be at a minimum 8 characters, contain one capital, and one number
```

### Set Permissions

```sh
chown -R www-data:www-data *
```

### Queue Listeners

Crontab Configuration

```sh
sudo crontab -e
```

```sh
* * * * * php /var/www/pterodactyl/artisan schedule:run >> /dev/null 2>&1
#Paste the above line at the bottom
```

### Create Queue Worker

```sh
cd /etc/systemd/system
```

```sh
nano pteroq.service
```

Add below contents to the file

```sh
# Pterodactyl Queue Worker File
# ----------------------------------

[Unit]
Description=Pterodactyl Queue Worker
After=redis-server.service

[Service]
# On some systems the user and group might be different.
# Some systems use `apache` or `nginx` as the user and group.
User=www-data
Group=www-data
Restart=always
ExecStart=/usr/bin/php /var/www/pterodactyl/artisan queue:work --queue=high,standard,low --sleep=3 --tries=3

[Install]
WantedBy=multi-user.target
```

```sh
sudo systemctl enable --now redis-server
```

```sh
sudo systemctl enable --now pteroq.service
```

You are now done with the Pterodactyl Panel Install and can moveon to the [Webserver Configuration](3%20-%20Webserver%20Configuration.md)