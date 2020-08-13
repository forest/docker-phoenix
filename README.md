# Dockerized Elixir/Phoenix Development Environment

![GitHub tag (latest SemVer)](https://img.shields.io/github/v/tag/forest/docker-phoenix)

## Introduction

This project deals with the issues of running different Elixir and Phoenix versions and supporting the development of
apps built with different Elixir and Phoenix versions.

## Assumptions

This project assumes you have [asdf](https://asdf-vm.com/) and [direnv](https://direnv.net/) installed and configured.

### Getting Started

It's so simple: just clone/download this repository.

You can specify a particular Phoenix version by targeting the corresponding release tag of this repository.

For instance, for a dockerized development environment for Phoenix 1.5.4 you could run:

```
git clone -b 1.5.4 https://github.com/forest/docker-phoenix ~/code/hello-phoenix
```

### Install Tools

```
asdf install
```

### Custom Commands

These scripts help to make working with docker-compose easier.

```
ls ./bin

app-db-up
app-destroy
app-e2e
app-restart
app-stop
app-up
mix
npm
run
yarn
```

### New Application from Scratch

Navigate the to where you cloned this repository, for example:

```
cd ~/code/hello-phoenix
```

Initialize a new phoenix application. The following command will create a new Phoenix application called `hello` under
the `current` directory, which is mounted inside the container under `/app` (the default work dir).

```
mix phx.new . --app hello
```

Why does this work? The `docker-compose.yml` file specifies that your local `current` directory is mapped inside the
docker container as `/app`. And `/app` in the container is marked as the working directory for any command that is being
executed, such as `mix phx.new`.

Find and Replace `myapp` with the name of you app.

**NOTE:** It is important to specify your app name through the `--app <name>` option, as Phoenix will otherwise name
your app from the target directory passed in, which in our case is `.`

### Database

#### Preparation

The `docker-compose.yml` file defines a database service container named `db` running a PostgreSQL database that is
available to the main application container via the hostname `db`. By default Phoenix assumes that you are running a
database locally.

Modify the Ecto configuration `src/config/dev.exs` to point to the DB container:

```
# Configure your database
config :test, Test.Repo,
  adapter: Ecto.Adapters.Postgres,
  username: "postgres",
  password: "postgres",
  database: "test_dev",
  hostname: "db",
  pool_size: 10
```

#### Initialize the Database with Ecto

When you first start out, the `db` container will have no databases. Let's initialize a development DB using Ecto:

```
mix ecto.create
```

If you copied an existing application, now would be the time to run your database migrations.

```
mix ecto.migrate
```

### Starting the Application

Starting your application is incredibly easy:

```
docker-compose up
-- or --
app-start
```

Once up, it will be available under http://localhost:4000

## Notes

### Executing custom commands

To run commands other than `mix` tasks, you can use the `run` script.

```
run iex -S mix
```

### Support for VS Code Remote Extension

After cloning this repository, open the folder in Visual Studio Code's Remote Extension to get a full Development
Environment (with PostgreSQL Database) spun up automatically.

See [https://code.visualstudio.com/docs/remote/containers](https://code.visualstudio.com/docs/remote/containers) for
more details.

### New with Elixir 1.9: Releases

Follow this [Github Gist](https://gist.github.com/forest/102f16359828405ce34ca083976986e1) to prepare a minimal Docker
release image based on Alpine Linux (about 38MB for a Phoenix Webapp).
