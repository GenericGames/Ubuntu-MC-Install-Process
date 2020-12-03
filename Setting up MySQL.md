# Setting up MySQL

[Back to README](README.md)

## Creating a database for Pterodactyl

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

After setting up the database for Pterodactyl you can go back to the installation

[Installation](2%20-%20Pterodactyl%20Panel%20install.md#installation)

## Creating a Database Host for Nodes

#Creating a user

```sh
mysql -u root -p
```

```sql
USE mysql;
```

Change IP below to server's IP & change username/password to unique user/pass

```sql
CREATE USER 'pterodactyluser'@'127.0.0.1' IDENTIFIED BY 'somepassword';
```

Assigning permissions

Change username and IP blow to whatever you used above

```sql
GRANT ALL PRIVILEGES ON *.* TO 'pterodactyluser'@'127.0.0.1' WITH GRANT OPTION;
```

```sql
FLUSH PRIVILEGES;
```

```sql
exit
```

Allowing external database access

```sh
cd /etc/mysql/
```

```sh
nano my.cnf
```

add below to file

```sh
[mysqld]
bind-address=0.0.0.0
```

```sh
systemctl restart mysql
```