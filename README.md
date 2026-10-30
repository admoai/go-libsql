# LibSQL package for Go

[libSQL](https://github.com/tursodatabase/libsql) is an open source, open contribution fork of SQLite.
This source repository contains libSQL API bindings for Go.

## Notice
This package comes with a precompiled native libraries.
Currently only `linux amd64`, `linux arm64`, `darwin amd64` and `darwin arm64` are supported.
We're working on adding support for more platforms.

## Features

* In-memory databases and local database files, like SQLite
* Remote database access to libSQL server
* In-app replica that syncs with a libSQL server

## Installing

```
go get github.com/tursodatabase/go-libsql
```

`go-libsql` uses `CGO` to make calls to LibSQL. You must build your binaries with `CGO_ENABLED=1`.

## Getting Started

### Connecting to the database

To connect to the database one needs to create a `libsql.Connector` using one of the factory functions: `libsql.NewEmbeddedReplicaConnector` or `libsql.NewEmbeddedReplicaConnectorWithAutoSync`.

Here's an example of obtaining a `sql.DB` object from `database/sql` package:

```
dbPath := // Path do db file on local disk
primaryUrl := // URL to primary database instance
connector := NewEmbeddedReplicaConnector(dbPath, primaryUrl, authToken)
db := sql.OpenDB(connector)
defer db.Close()
```

Once `sql.DB` object is created one can use it as any other database that supports `database/sql` package.

### Fetching updates from primary database instance

If the connector is created with `libsql.NewEmbeddedReplicaConnectorWithAutoSync` then it will automatically fetch updates from a primary periodically.

For connectors created with `libsql.NewEmbeddedReplicaConnector` we need to fetch updates manually by calling `connector.Sync`

## Examples

Module with usage examples can be found in [example directory].

## License

This project is licensed under the [MIT license].

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in libSQL by you, shall be licensed as MIT, without any additional
terms or conditions.

[MIT license]: https://github.com/tursodatabase/go-libsql/blob/main/LICENSE
[example directory]: https://github.com/tursodatabase/go-libsql/tree/main/example

## admoai fork

This fork carries `libsql-0.9.23` plus tursodatabase/libsql#2261 (the fix for the local connection
double close, tursodatabase/libsql#2251) until upstream releases it. The rebuilt static libraries
live on the patch branch `admoai/v0.9.23-fix-2261`. Tracking: admoai/adhub#5157.

### Provenance of the libraries on `admoai/v0.9.23-fix-2261`

The static libraries in commit `5b2c8ae` were built by this repository's `update-libsql.yml`, run
https://github.com/admoai/go-libsql/actions/runs/41064124818: `libsql-0.9.23` with
tursodatabase/libsql@`4e06af572ddf7e3b82a8609c4c623637bae0d778` cherry-picked, rustc/cargo 1.99.1,
cross 0.2.5. The run's build and verify jobs passed. The workflow token cannot push to this fork,
so the libraries were committed by hand from the run's artifacts. Their SHA-256, identical as
downloaded and as committed:

| Library | SHA-256 |
|---|---|
| `lib/linux_amd64/libsql_experimental.a` | `568698ecc1dc9ccbfa526a9a806c1f03703dbd9f62d1b07cdb37e7f6b6d1a2cb` |
| `lib/linux_arm64/libsql_experimental.a` | `c47986567a6e003769b8ae85927359af3ce48a12aa40992062ffe26c9b3ef0a2` |
| `lib/darwin_arm64/libsql_experimental.a` | `d3b22ce7c6c1c6fdae7b49596cdbe3e7c8999f2d30d6d2220be6d531052008d9` |

`lib/include/libsql.h` is unchanged from libsql-0.9.23. To re-derive, dispatch `update-libsql.yml`
on that branch with `libsql_tag=libsql-0.9.23` and `cherry_pick=4e06af572ddf7e3b82a8609c4c623637bae0d778`.
