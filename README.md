# PostgreSQL 14 Installation and configuration on debian
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt install gnupg
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql-14
```
## Password modification : method 1
To change the password of postgreSQL **super user** do the following :
- **login as system postgres user** : 
```bash
sudo -i -u postgres
```

The **-i** (simulate initial login) option runs the shell specified by the password database entry of the target user as a login shell.  This means that login-specific resource files such as .profile or .login will be read by the shell.

- **use the psql client program to change database postgres user password**: 
```bash
psql -c "ALTER USER postgres WITH password 'myNewPassword'"
```
## Password modification : method 2
To change the password of postgreSQL **super user** do the following :
- **login as system postgres user** : 
```bash
sudo -i -u postgres
```
- Connect to the local server using “psql”
- Type the “\password” meta command of psql

```sql
postgres=# \password
Enter new password:
Enter it again:
postgres=#
```

## PostgreSQL configuration file
File Path: /etc/postgresql/14/main/pg_hba.conf

If you want to be able to connect remotely to your PostgreSQL you have to change the listening address :

```#listen_addresses = 'localhost'``` to ```listen_addresses = '*'```

If you want to change the listening port which requires a restart of PostgreSQL service:
```
port = 12345
```
## PostgreSQL Client Authentication Configuration File
File Path: /etc/postgresql/14/main/postgresql.conf

If you want to allow any valid user to connect remotely to your PostgreSQL Database server add this line :

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD
host    all             all             0.0.0.0/0            scram-sha-256
```
# Administration

## listing all active connections
```sql
SELECT pid, datid, datname, usesysid, usename , query, client_addr, client_port, query_start, backend_start, wait_event, xact_start, state FROM pg_catalog.pg_stat_activity
```

The pid column indicates session id. This pid value can then be passed to the pg_terminate_backend command to terminate the session. Here is an example:
```sql
# replace pid by your pid number
SELECT pg_terminate_backend(pid);
```
## Dump and restore

```sql
# to dump a database into file
pg_dump database_name  file_name.sql
```

```sql
# to restore previously dumped database
psql -U postgres -d database_name -f file_name.sql
```
## Get active connections number

```sql
select max_conn, used, res_for_super, max_conn-used-res_for_super res_for_normal 
from   (select count(*) used from pg_stat_activity) t1,
  (select setting::int res_for_super from pg_settings where name=$$superuser_reserved_connections$$) t2,
  (select setting::int max_conn from pg_settings where name=$$max_connections$$) t3
```

Output :

```
max_conn | used | res_for_super | res_for_normal 
----------+------+---------------+----------------
      200 |   30 |             3 |            167
```
