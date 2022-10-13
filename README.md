# PostgreSQL system role
![CI Testing](https://github.com/linux-system-roles/postgresql/workflows/tox/badge.svg)

The PostgreSQL system role installs, configures, and starts the PostgreSQL server.

The role also optimizes the database server settings to improve performance.

The role currently works with the PostgreSQL server versions 10, 12, and 13.

## Role Variables
### postgresql_verison
You can set the version of the PostgreSQL server to 10, 12, or 13.
```yaml
postgresql_version: "13"
```
### postgresql_password
Optionally, you can set a password for the `postgres` database superuser. By default, no password is set, and a datababase is accessible from the `postgres` system account through a UNIX socket.
It is recommended to encrypt the password using Ansible Vault.
```yaml
postgresql_password: !vault |
          $ANSIBLE_VAULT;1.2;AES256;dev
          ....
```
### postgresql_pg_hba_conf
The content of the `postgresql_pg_hba_conf` variable replaces the default upstream configuration in the `/var/lib/pgsql/data/pg_hba.conf` file.
```yaml
postgresql_pg_hba_conf:
  - type: local
    database: all
    user: all
    auth_method: peer
  - type: host
    database: all
    user: all
    address: '127.0.0.1/32'
    auth_method: ident
  - type: host
    database: all
    user: all
    address: '::1/128'
    auth_method: ident
```
### postgresql_server_conf
The content of the `postgresql_server_conf` variable is added to the end of the `/var/lib/pgsql/data/postgresql.conf` file. As a result, the default settings are overwritten.
```yaml
postgresql_server_conf:
  ssl: on
  shared_buffers: 128 MB
  huge_pages: try
```
### postgresql_ssl_enable
To set up a SSL/TLS connection, set the `postgresql_ssl_enable` variable to `true` and provide a server certificate and a private key.
```yaml
postgresql_ssl_enable: true
```
### postgresql_cert_name
Use the `postgresql_cert_name` variable to specify the certificate name.
You can copy your server certificate to `/etc/pki/tls/certs/server.crt` and your private key to `/etc/pki/tls/private/server.key`. Alternatively, you can use the certificate system role. For details, see the [`examples/`](examples).
```yaml
postgresql_cert_name: server
```
### postgresql_key_path
Optionally, you can specify a path to the server key using the `postgresql_key_path` variable. The default value is `/etc/pki/tls/private`.
```yaml
postgresql_key_path: "/etc/pki/tls/private"
```
### postgresql_cert_path
Optionally, you can specify a path to the server certificate using the `postgresql_cert_path` variable. The default value is `/etc/pki/tls/certs`.
```ymal
postgresql_cert_path: "/etc/pki/tls/certs"
```
### postgresql_input_file
To run an SQL script, define a path to your SQL file using the `postgresql_input_file` variable.
```yaml
postgresql_input_file: "/tmp/mypath/file.sql"
```
### postgresql_server_tuning
By default, the PostgreSQL system role enables server settings optimization based on system resources. To disabe the tuning, set the `postgresql_server_tuning` variable to `false`.
```yaml
postgresql_server_tuning: false
```

See the [`examples/`](examples) directory for details.


## Example Playbook


```yaml
- hosts: all
  vars:
    postgresql_version: "13"
    postgresql_password: !vault |
          $ANSIBLE_VAULT;1.2;AES256;dev
          ....

  roles:
    - linux-system-roles.postgresql
```

You can find more examples in the [`examples/`](examples) directory.

## License

MIT
