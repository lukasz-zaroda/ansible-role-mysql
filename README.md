Ansible role: MySQL
===================
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-mysql-000000.svg)](https://github.com/lukasz-zaroda/ansible-role-mysql)

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

For first two: the keys on the first level are users/databases names, their values are dictionaries of settings.
For example:

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
In the above example the "otherdatabase" has no settings set. When the setting is not set for a database, or user,
the default values will be used. These default values will come from the dictionaries: `mysql_databases_defaults`/`mysql_users_defaults`.
This is an example of the `mysql_users_defaults` dictionary:

```yml
mysql_databases_defaults:
  encoding: utf8mb4
  collation: utf8mb4_unicode_ci
```

^ This way all databases, that have no encoding and collation set, will get these defaults. Defaults for users work
exactly the same, but come from the `mysql_users_defaults` dictionary.

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

There is also a dictionary with settings for a root account: mysql_root, for example:

```yml
mysql_root:
  password: hunter2
```

It works like user from the mysql_users dictionary, but is separated, and is not filled with defaults from the
`mysql_users_defaults`. Also note that you don't have to store the root password in your playbook, after you
will set it once, root credentials will be saved on the target system, and no longer will have to be referenced by
the playbook for it to work.

There are also some other variables that should be tweaked for specific system you are deploying to. Defaults are
made for Debian.

```yml
mysql_daemon: mysql
mysql_config_path: /etc/mysql/conf.d
mysql_daemon_manager: systemd # available values: systemd, service
mysql_packages: [mysql-common, mysql-server, python-mysqldb]
mysql_package_manager: apt # available values: apt, yum
```

Dependencies
------------

None.

Example Playbook
----------------

```yml
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