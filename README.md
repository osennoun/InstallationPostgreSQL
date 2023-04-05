# PostgreSQL 14 Installation and configuration on debian
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql-14
```
## Password modification
To change the password of postgreSQL **super user** do the following :
- **login as postgres user** : ```sudo -i -u postgres```

The **-i** (simulate initial login) option runs the shell specified by the password database enry of the target user as a login shell.  This means that login-specific resource files such as .profile or .login will be read by the shell.

- **use the psql client program to change postgres user password**: ```psql -c "ALTER USER postgres WITH password 'myNewPassword'"```
## PostgreSQL configuration file
## PostgreSQL Client Authentication Configuration File
