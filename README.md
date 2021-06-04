**THIS IS A WORK IN PROGRESS. USE AT YOUR OWN RISK.**

[![Published on Hex](https://img.shields.io/hexpm/v/snowflex)](https://hex.pm/packages/snowflex)
[![License Info](https://img.shields.io/hexpm/l/snowflex)](https://github.com/pepsico-ecommerce/snowflex/blob/master/LICENSE)

# Snowflex ❄💪

This application encapsulates an ODBC connection pool for connecting to the Snowflake data warehouse.

## Setup

The following config options can be set:

```elixir
config :snowflex,
  driver: "/path/to/my/ODBC/driver" # defaults to "/usr/lib/snowflake/odbc/lib/libSnowflake.so"
```

Connection pools are not automatically started for you. You will need to define and establish each connection pool in your application module. Configuration values related to connection timeouts and the mapping of `:null` query values can be set here.

First, create a module to hold your connection information:

```elixir
defmodule MyApp.SnowflakeConnection do
  use Snowflex.Connection,
    otp_app: :my_app,
    timeout: :timer.minutes(20),
    map_nulls_to_nil?: true
end
```

Define your configuration:

```elixir
import Config

# ...

config :my_app, MyApp.SnowflakeConnection,
  connection: [
      role: "PROD",
      warehouse: System.get_env("SNOWFLAKE_POS_WH"),
      uid: System.get_env("SNOWFLAKE_POS_UID"),
      pwd: System.get_env("SNOWFLAKE_POS_PWD")
    ]

 # you may define multiple connection modules
 config :my_app, MyApp.MyOtherSnowflakeConnection,
    worker: MyApp.MockWorker # defaults to Snowflex.Worker (change for testing/development)
    connection: [
      role: "PROD",
      warehouse: System.get_env("SNOWFLAKE_ADVERTISING_WH"),
      uid: System.get_env("SNOWFLAKE_ADVERTISING_UID"),
      pwd: System.get_env("SNOWFLAKE_ADVERTISING_PWD")
    ]
```

The odbc driver will, by default, return `:null` for empty values returned from snowflake queries.
This will be converted to `nil` by default by Snowflex. A configuration value `map_nulls_to_nil?`
can be set to `false` if you do not desire this behavior.

Then, in your application module, you would start your connection:

```elixir
def MyApp.Application do
  use Application

  def start(_type, _args) do

    children = [
      MyApp.SnowflakeConnection,
      MyApp.MyOtherSnowflakeConnection
    ]

    opts = [strategy: :one_for_one, name: MyApp.Supervisor]
    Supervisor.start_link(children, opts)
  end
end
```

## Usage

In order to execute queries, you will need to create a `Snowflex.Query` struct and pass it to your execute function. It would look something like this for a static query.

```elixir
{:ok, query} = Snowflex.Query.create(%{query_string: "SELECT * FROM my_table"})
MyApp.SnowflakeConnection.execute(query)
```

The `Query.create/1` function will also attempt to cast your SQL params.

```elixir
# This will cast the params to [{:sql_integer, 1}]
{:ok, query} = Snowflex.Query.create(%{query_string: "SELECT * FROM my_table WHERE id = ?", params: [1]})

MyApp.SnowflakeConnection.execute(query)
```

## Caveats

If you are planning to connect to the Snowflake warehouse, your local Erlang instance
must have ODBC enabled. The erlang installed by Homebrew does NOT have ODBC support. The `asdf`
version of erlang does have ODBC support. You will also need the Snowflake ODBC driver installed
on your machine. You can download this from https://sfc-repo.snowflakecomputing.com/odbc/index.html.

## Installation

The package can be installed by adding `snowflex` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:snowflex, "~> 0.3.2"}
  ]
end
```
