MySQL
=========

MySQL installation, configuration, and easy user/database management.

Requirements
------------

None.

Role Variables
--------------

Variables of this role are based on dictionaries. There are several levels of dictionaries that will be in the end
merged together creating final configurations. There are three main dictionaries:
- mysql_databases
- mysql_users
- mysql_config

For two first: the keys on the first level are users/databases names, the values are dictionaries of settings. Example:

```yml
mysql_databases:
  mydatabase:
    encoding: utf8mb4
    collation: utf8mb4_unicode_ci
  otherdatabase: ~

mysql_users:
  myuser:
    password: hunter2
    priv: "mydatabase.*:ALL"
```
In the above example "otherdatabase" has no settings set. When the setting is not set for database, or user,
the default setting is being used, from the dictionaries `mysql_databases_defaults`/`mysql_users_defaults` ,
looking like this:

```yml
mysql_databases_defaults:
  encoding: utf8mb4
  collation: utf8mb4_unicode_ci
```

^ This way all databases, that have no encoding and collation set, will get those defaults. Defaults for users works
exactly the same.

You can see available settings here:
http://docs.ansible.com/ansible/mysql_user_module.html
http://docs.ansible.com/ansible/mysql_db_module.html

Be aware what you are setting. You can do everything, but not everything is good for you. ;)

mysql_config is a simple dictionary of MySQL settings.

```yml
mysql_config:
  mysqld:
    key_buffer_size: "256M"
    max_allowed_packet: "64M"
```
^ This will be turned into:

```ini
[mysqld]
key_buffer_size=256M
max_allowed_packet=64M
```

There is also a dictionary with a settings for a root account: mysql_root, for example:

```yml
mysql_root:
  password: hunter2
```

It works like user from the mysql_users table, but is separated, and is not influenced by the `mysql_users_defaults`
dictionary. Also note that you don't have to store the root password in your playbook, after you will set it once,
root credentials will be saved on the target system, and no longer will have to be referenced by the playbook, for it
to work.

There are also some other variables that should be tweaked for specific system you are deploying to. Defaults are
made for Debian based systems.

```yml
mysql_daemon: mysql
mysql_config_path: /etc/mysql/conf.d
mysql_daemon_manager: systemd # available values: systemd, service
mysql_packages: [mysql-common, mysql-server]
mysql_package_manager: apt # available values: apt, yum
```

Dependencies
------------

None.

Example Playbook
----------------

```
---
- hosts: servers
  become: true
  become_method: sudo
  roles:
  - role: mysql

    mysql_root:
      password: root

    mysql_databases_defaults:
      encoding: utf8mb4
      collation: utf8mb4_unicode_ci

    mysql_users_defaults:
      priv: "*.*:USAGE"

    mysql_databases:
      mydatabase: ~

    mysql_users:
      example_user:
        password: hunter2
        priv: "mydatabase.*:ALL"

    mysql_config:
      mysqld:
        key_buffer_size: "128M"
        max_allowed_packet: "32M"
```

License
-------

MIT

Author Information
------------------

Łukasz Zaroda - https://luken-tech.pl