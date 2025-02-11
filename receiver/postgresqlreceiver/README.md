# PostgreSQL Receiver

| Status                   |           |
| ------------------------ |-----------|
| Stability                | [beta]    |
| Supported pipeline types | metrics   |
| Distributions            | [contrib] |

This receiver queries the PostgreSQL [statistics collector](https://www.postgresql.org/docs/9.6/monitoring-stats.html).

> :construction: This receiver is in **BETA**. Configuration fields and metric data model are subject to change.

## Prerequisites

This receiver supports PostgreSQL versions 9.6+

The monitoring user must be granted `SELECT` on `pg_stat_database`.

## Configuration

The following settings are required to create a database connection:
- `username`
- `password`

The following settings are optional:
- `endpoint` (default = `localhost:5432`): The endpoint of the postgresql server. Whether using TCP or Unix sockets, this value should be `host:port`. If `transport` is set to `unix`, the endpoint will internally be translated from `host:port` to `/host.s.PGSQL.port`
- `transport` (default = `tcp`): The transport protocol being used to connect to postgresql. Available options are `tcp` and `unix`.

- `databases` (default = `[]`): The list of databases for which the receiver will attempt to collect statistics. If an empty list is provided, the receiver will attempt to collect statistics for all non-template databases.

The following settings are also optional and nested under `tls` to help configure client transport security
- `insecure` (default = `false`): Whether to enable client transport security for the postgresql connection.
- `insecure_skip_verify` (default = `true`): Whether to validate server name and certificate if client transport security is enabled.
- `cert_file` (default = `$HOME/.postgresql/postgresql.crt`): A cerficate used for client authentication, if necessary.
- `key_file` (default = `$HOME/.postgresql/postgresql.key`): An SSL key used for client authentication, if necessary.
- `ca_file` (default = ""): A set of certificate authorities used to validate the database server's SSL certificate.

- `collection_interval` (default = `10s`): This receiver collects metrics on an interval. This value must be a string readable by Golang's [time.ParseDuration](https://pkg.go.dev/time#ParseDuration). Valid time units are `ns`, `us` (or `µs`), `ms`, `s`, `m`, `h`.

### Example Configuration

```yaml
receivers:
  postgresql:
    endpoint: localhost:5432
    transport: tcp
    username: otel
    password: $POSTGRESQL_PASSWORD
    databases:
      - otel
    collection_interval: 10s
    tls:
      insecure: false
      unsecure_skip_verify: false
      ca_file: /home/otel/authorities.crt
      cert_file: /home/otel/mypostgrescert.crt
      key_file: /home/otel/mypostgreskey.key
```

The full list of settings exposed for this receiver are documented [here](./config.go) with detailed sample configurations [here](./testdata/config.yaml). TLS config is documented further under the [opentelemetry collector's configtls package](https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/configtls/README.md). 

## Metrics

Details about the metrics produced by this receiver can be found in [metadata.yaml](./metadata.yaml)

[beta]: https://github.com/open-telemetry/opentelemetry-collector#beta
[contrib]: https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol-contrib