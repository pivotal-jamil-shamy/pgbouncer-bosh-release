# PGBouncer BOSH Release

## Usage:

This configuration:

```
  - name: pgbouncer
    release: pgbouncer
    properties:
      database:
        client_side_db_name: clientDB
        dbname: atc
        host: 10.0.1.1
        port: 5432
      pgbouncer:
        listen_addr: 127.0.0.1
        listen_port: 8888
        admin_users: atc-user
        default_pool_size: 32
        max_client_conn: 64
      auth_file:
        user: atc-user
        password: meow
```

will yield this pgbouncer config file:

```
[databases]
clientDB = host=10.0.1.1 port=5432 dbname=atc

[pgbouncer]
listen_port = 8888
listen_addr = 127.0.0.1
auth_type = md5
auth_file = /var/vcap/jobs/pgbouncer/config/pgbouncer-users
admin_users = atc-user
pool_mode = session
default_pool_size=32
max_client_conn=64
ignore_startup_parameters = extra_float_digits
```

and this pgbouncer users file:

```
"atc-user" "meow"
```

## Links:

PGBouncer job consumes this link:

```
consumes:
- name: postgres
  type: database
  optional: true
```

When the link is available, you do not need to specify the `database.host` property. The link will automatically figure out the DB VM IP address. So the PGBouncer properties will look like this (notice that we will not specify  `database.host` property):

```
  - name: pgbouncer
    release: pgbouncer
    properties:
      database:
        client_side_db_name: clientDB
        dbname: atc
        port: 5432
      pgbouncer:
        listen_addr: 127.0.0.1
        listen_port: 8888
        admin_users: atc-user
        default_pool_size: 32
        max_client_conn: 64
      auth_file:
        user: atc-user
        password: meow
```

Note: if the link is available, and you want to explicitly specify the host, you can always provide `database.host` in the properties, it will override any links addresses.

## Create a release:

To create a release with version `0.0.15`, clone this repo, then:

```
bosh create-release --force --tarball=./pgbouncer-0.0.15.tgz --version=0.0.15
```

Bump the version up if you modify the release and want to create a new version.
