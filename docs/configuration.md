# Configuration Options

## Updating Service Plans

Updating the service instances is supported; see [Service plans and instances](docs/service-plans-instances.md) for details.

## Read-Only Administrator User

The manifest optionally allows the user to specify a password for the `roadmin` user. By supplying this password, the service will automatically create a user that has access to read all databases, but permission to write to none of them.

This parameter is defined in the [spec file](../jobs/mysql/spec).

## Pre-seeding Databases

Normally databases are created via the `cf create-service` command, and
a MySQL user is created and given access to that database when an app is bound to that service instance.
However, it is sometimes useful to have databases and users already available when the service is deployed,
without having to run `cf create-service` or bind an app.
To specify any preseeded databases, add the following to the deployment manifest:

```
jobs:
- name: mysql_z1
  properties:
    seeded_databases:
    - name: db1
      username: user1
      password: pw1
    - name: db2
      username: user2
      password: pw2
```

Note: If all you need is a database deployment, it is possible to deploy this
release with zero broker instances and completely remove any dependencies on Cloud Foundry.
See the [proxy](jobs/proxy/spec) and [acceptance-tests](jobs/acceptance-tests/spec) spec files for standalone configuration options.

## Configuring how long the startup script waits for the database to come online

On larger databases, the default database startup timeout may be too low.
This would result in the job reporting as failing, while MySQL continues to bootstrap in the background (see [Known Issues > Long SST Transfers](docs/Known-Issues.md#long-sst-transfers)).
To increase the duration that the startup script waits for MySQL to start, add the following to your deployment stub:

```yaml
jobs:
- name: mysql_z1
  properties:
    database_startup_timeout: 360
```

Note: This is independent of the overall BOSH timeout which is also configurable in the manifest. The BOSH timeout should always be higher than the database startup timeout:

```yaml
update:
  canary_watch_time: 30000-600000
  update_watch_time: 30000-600000
```
