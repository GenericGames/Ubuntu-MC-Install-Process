# Setting up MySQL

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
