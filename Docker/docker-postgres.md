# PostSQL in Docker

## Benefits of running PostgreSQL in a Docker container

With Docker, you can spin up a PostgreSQL instance with just one command. No
more dealing with package managers, system dependecies, or complicated scripts.
The database comes pre-configured with sensible defaults and is ready to use
immediately.

Everyone on your team gets exactly the same PostgreSQL setup, regardless of
their operating system.

Docker containers run in isolation, which means your PostgreSQL instance wont
interfere with other software on your machine. You can run multiple versions of
PostgreSQL at the same time without conflicts, and removing PostgreSQL is as
simple as stopping and removing the container.

Docker allows you to specify exact PostgreSQL versions in your configuration
files. This means you can make sure development environments match production
exactly, and you can test upgrades in isolation before applying them to
production systems.

Docker containers are lightweight compared to virtual machines. They are fewer
system resources and provide similar isolation benefits. This means you can run
PostgreSQL alongside other containers without significantly impacting system
performance.

## Docker volumes on persisting PostgreSQL data

Volumes are persistent data stores for containers, created and managed by
Docker. Since container file systems are temporary, any data stored inside them
is permanently lost when the container is stopped or removed. Volumes decouple
data from the container lifecycle and store it safely on the host machine.

Docker volumes work by bypassing the container's temporary copy-on-write
filesystem and writing data directly to a dedicated directory on the host
machine's filesystem.

**Storage Architecture**

```
[ Container Filesystem ]
└── /app/data  <-- (Mount Point)
         │
         ▼  [ Bypasses the layered filesystem ]
┌──────────────────────────────────────────────┐
│             Docker Storage Engine            │
└──────────────────────────────────────────────┘
         │
         ▼  [ Writes directly to Host OS ]
[ Host Filesystem ]
└── /var/lib/docker/volumes/my_volume/_data
```

## How can you connect to a running PostgreSQL container?

You can connect to a running PostgreSQL Docker container using:

```bash
docker exec -it postgres-dev psql -U postgres 
```

- docker exec — executes a command inside a running container.
- -it — provides an interactive terminal.
- postgres-dev — the name of the PostgreSQL container.
- psql — starts the PostgreSQL command-line client.
- -U postgres — connects using the postgres user.