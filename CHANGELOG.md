# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## Breaking

- add `Snowflex.Query` as the way to create queries and pass data.
- remove `Snowflex.sql_query` and `Snowflex.param_query` in favor of using a single `Snowflex.run_query` function that uses the new `Snowflex.Query`.

## Added

- `Snowflex.Connection.execute` now accepts a keyword list of options to override `Snowflex.Connection`-level options on a per query basis.
- Updated deps

## [0.3.2] - 2021-06-02

### Added

- Added `map_nulls_to_nil?` variable to connection configuration to allow conversion of `:null` values to `:nil` in snowflake query response

## [0.3.1] - 2021-03-10

### Fixed

- Initialize the worker with a propslist instead of a tuple.
- create copies of the `odbc` type definitions

## [0.3.0] - 2021-03-09

### Added

- Added the ability to keep connections alive through configuration on the `Snowflex.Connection` module.

## [0.2.2] - 2021-02-09

### Fixed

- make sure to follow `{:error, reason}` convention in all parts of the worker

## [0.2.1] - 2020-11-20

### Fixed

- fix bug where handle_info is missing argument

## [0.2.0] - 2020-11-18

### Added

- Added the ability to define a module that will use `Snowflex.Connection` to maintain connection information

### Breaking

- Remove `Snowflex.Query` as it was a duplicate
- Remove `Snowflex.ConnectionPool` in favor of new `Snowflex.Connection`

## [0.1.1] - 2020-11-17

### Changed

- Configure the worker module on the `Snowflex.ConnectionPool` and no longer on the application level.

## [0.1.0] - 2020-11-16

### Breaking

- Users must launch connection pools as part of their application's
  supervision tree using `Snowflex.ConnectionPool`, and all queries must specify
  which connection pool to use. No connection pool will be started by default.
