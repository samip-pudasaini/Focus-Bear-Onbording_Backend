# Docker Debugging

## docker inspect

Docker inspect provideds detailed information on constructs controlled by
Docker. By default `docker inspect` will render result in a JSON array.

| Option | Description | | `-f, --format ` | If a format is specified, the given
template will be executed for each result. | | `-s, --size` | Display total file
sizes if the type is container | | `--type` | Only inspect objects of the given
type |

**Examples** `docker inspect --type=volume myvolume`

`docker inspect --size mycontainer`

Getting an instance's image name

`docker inspect --format='{{.Config.Image}}' $INSTANCE_ID`

## docker ps

Lists containers for a Compose project, with current status and exposed ports.

    ```
    docker compose ps
    NAME            IMAGE     COMMAND           SERVICE    CREATED         STATUS          PORTS
    example-foo-1   alpine    "/entrypoint.â¦"   foo        4 seconds ago   Up 2 seconds    0.0.0.0:8080->80/tcp
    ```

By default, only running containers are shown. --all flag can be used to include
stopped containers.

    ```
    docker compose ps --all
    NAME            IMAGE     COMMAND           SERVICE    CREATED         STATUS          PORTS
    example-foo-1   alpine    "/entrypoint.â¦"   foo        4 seconds ago   Up 2 seconds    0.0.0.0:8080->80/tcp
    example-bar-1   alpine    "/entrypoint.â¦"   bar        4 seconds ago   exited (0)
    ```

## docker logs

Displays log output from services.

| Option             | Description                                                         |
| ------------------ | ------------------------------------------------------------------- |
| `-f, --follow`     | Follow log output                                                   |
| `--index`          | Index of the container if service has multiple replicas             |
| `--no-color`       | Produce monochrome output                                           |
| `--no-log-prefix`  | Don't print prefix in logs                                          |
| `--since`          | Show logs since a timestamp or relative time (e.g. `42m`)           |
| `-n, --tail`       | Number of lines to show from the end of the logs for each container |
| `-t, --timestamps` | Show timestamps                                                     |
| `--until`          | Show logs before a timestamp or relative time (e.g. `42m`)          |

## docker exec -it

`docker exec -it` command is used to open an interactive terminal session inside
a currently running Docker container. It allows you to explore the container's
file system, run diagnostic tools, and debug applications directly inside its
environment.

`docker exec -it <container_name_or_id> <shell_executable>`

- `exec`: Tells Docker to run a new process inside an existing, running
  container.
- `-i` (interactive): Keeps the standard input (stdin) open so you can type
  commands.
- `-t` (tty): Allocates a pseudo-TTY, which provides the terminal prompt
  interface

## docker stop

`docker stop <container>` It sends a SIGTERM signal to let the app shut down
gracefully. The container stops running but remains in memory/disk. Files
written outside of volumes are preserved.

## docker rm

`docker rm <container>` Permanently deletes a stopped container. Erases the
container filesystem. Any data not stored in a persistent Docker volume is
permanently lost. **Use case**: Cleaning up old, unused container definitions to
free up disk space.

## docker-compose down && up

`docker-compose down` Stops all running services defined in the file, then
automatically removes the containers and their dedicated virtual networks.
Cleans the environment completely. Named volumes are kept by default unless you
explicitly add the -v flag. Used for Tearing down a local development stack when
you are done working for the day.

`docker-compose up` Downloads required images, builds custom Dockerfiles,
creates networks, attaches volumes, and launches all services from scratch.Used
to Spin up your entire project stack (app, database, cache) with a single
command.

## Reflection

### 2. What is the difference between `docker exec` and `docker attach`? Command Purpose
   `docker exec` Starts a new process inside an already-running container. docker
   attach Connects your terminal to the main process (PID 1) of the container.

For debugging, `docker exec` is generally safer because it opens a separate
process without directly interacting with the container's main process.

Example:

`docker exec` -it <container> sh 3. How do you restart a container without losing
data?

Use:

`docker restart <container>`

Restarting a container does not remove its Docker volumes, so data stored in
persistent volumes remains available.

For example:

`docker restart postgres-dev`

Important data should be stored in Docker volumes rather than only inside the
container's writable filesystem.

### 4. How can you troubleshoot database connection issues inside a containerized
   NestJS app?

A useful troubleshooting process is:

Check that the database container is running:

`docker compose ps`

Check the database logs:

`docker logs <database-container>`

Check the NestJS application logs:

`docker logs <nestjs-container>`

Check the database connection environment variables:

`docker exec -it <nestjs-container> env`

Check the database hostname.

If the PostgreSQL service in compose.yaml is called postgres, the NestJS
application should normally use:

`DB_HOST=postgres`

localhost should generally not be used when PostgreSQL is running in a separate
container because localhost refers to the NestJS container itself.

Test connectivity from inside the NestJS container:

`docker exec -it <nestjs-container> sh`

From inside the container, use an available networking or database client to
test the connection.

Check that both containers are connected to the same Docker network.

A common cause of database connection errors is using localhost as the database
host when the database is running in a separate container. In Docker Compose,
the service name can be used as the hostname for communication between
containers.