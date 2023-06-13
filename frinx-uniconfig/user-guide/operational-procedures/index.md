# Operational Procedures

## Logging

The UniConfig distribution uses Logback as its logging
framework. Logback is the successor to the log4j framework with many
improvements, such as more options for configuration, better performance,
and context-based separation of logs. Context-based separation of logs
is used widely in UniConfig to achieve per-device logging based on the
set marker in the logs.

[!ref text="Logging"](../operational-procedures/logging)

## TLS

TLS is a widely adopted security protocol designed to facilitate
privacy and data security for communications over the Internet. TLS authentication is disabled
in the default version of UniConfig.

[!ref text="TLS"](../operational-procedures/tls)

## TLS for Postgres database

By default, UniConfig communicates with the database without TLS and traffic is therefore unencrypted.
When the database is deployed separately from UniConfig, we recommend that you enable TLS encryption.


[!ref text="TLS for Postgres database"](../operational-procedures/postgres-tls)

## OpenAPI

The UniConfig distribution contains a '.yaml' file that generates list of all
usable RPCs with examples. You can view it either locally or on our hosted version, which always shows
the latest OpenAPI version.

[!ref text="OpenAPI"](../operational-procedures/openapi)

## Data Security Models

UniConfig supports encryption and hashing of values in RESTCONF and
UniConfig shell API, as well as managing confidential data during transfers
between the UniConfig database and network devices.

[!ref text="Data Security Models"](../operational-procedures/data-security-models)

## UniConfig Clustering

The UniConfig stateless architecture allows deployment of the system in a cluster to ensure
horizontal scalability and high-availability properties.

[!ref text="UniConfig Clustering"](../operational-procedures/uniconfig-clustering)

## Thread pools

UniConfig uses thread pools in several places. They can be configured in the
application.properties file.

[!ref text ="Thread pools"](../operational-procedures/thread-pools)
