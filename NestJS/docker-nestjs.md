# Dockerfile

The dockerfile is a text file that contains the instructions Docker uses to
build your container image.

```
# Use the official Node.js image as the base image
FROM node:20

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy package.json and package-lock.json to the working directory
COPY package*.json ./

# Install the application dependencies
RUN npm install

# Copy the rest of the application files
COPY . .

# Build the NestJS application
RUN npm run build

# Expose the application port
EXPOSE 3000

# Command to run the application
CMD ["node", "dist/main"]
```

This is a basic Dockerfile that sets up a Node.js environment, installs the
application dependencies, builds the NestJS application, and runs it. You can
customize this file based on your project requirements.

For .dockerignore file that specifies which files and directories Docker should
ignore when building the image.

```
node_modules
dist
*.log
*.md
.git
```

To set up a Dockerfile: `docker build -t my-nestjs-app`

- -t my-nestjs-app: Tags the image with the name my-nestjs-app.
- .: Indicates the current directory as the build context.

After building the image, you cna run it as a container. Execute the following
command: `docker run -p 3000:3000 my-nestjs-app`

- -p 3000:3000: Maps port 3000 on your host machine to port 3000 in the
  container.
- my-nestjs-app: Specifies the image to run.

# Multi-Stage builds

With multi-stage builds, you can multiple `FROM` statements in your Dockerfile.
Each `FROM` instruction can use a different base, and each of then begins a new
stage of the build. You can selectively copy artifacts from one stage to
another, leaving behind everything you dont want in the final image

Dockerfile with 2 separate stages: one for building in binary, and another where
the binary gets copied from the first stage into the next stage.

```
# syntax=docker/dockerfile:1
FROM golang:1.26
WORKDIR /src
COPY <<EOF ./main.go
package main

import "fmt"

func main() {
  fmt.Println("hello, world")
}
EOF
RUN go build -o /bin/hello ./main.go

FROM scratch
COPY --from=0 /bin/hello /bin/hello
CMD ["/bin/hello"]
```

You only need the single Dockerfile. No need for a separate build script. Just
run `docker build`.

The end result is a tiny production image with nothing but the binary inside.
None of the build tools required to build the application are included in the
resulting image.

How does it work? The second `FROM` instruction starts a new build stage with
the `scratch` image as its base. The `COPY --from=0` line copies just the built
artifact from the previous stage into this new stage. The Go SDK and any
intermediate artifacts are left behind, and not saved in the final image.

# Multi-Container with Docker Compose

It a tool for defining and running multicontainer Docker applications enabling
users to configure their application services in a single yaml file.

It simplifies the process of managing and deploying complex applications by
allowing you to start stop and manage containers with a single command.

As containers are isolated, and an application might have multiple container.
For example, one can be database, another Frontend, another Backend.

Despite the number of containers, once the compose is implemented in the
application, the developers can use it easily by just: git clone -> docker
compose up -> Do cool stuff.

If you change something you can just use docker compose up and it will reconcile
the changes in your file and apply them intelligently.

Without compose, you would need to run each docker container individually and
also clean up individually.

# Debugging

| **Action**                                           | **Command**                                     |
| ---------------------------------------------------- | ----------------------------------------------- |
| **View all logs for a container**                    | `docker logs <container>`                       |
| **Follow logs in real-time (tail)**                  | `docker logs -f <container>`                    |
| **Tail the last 100 lines**                          | `docker logs --tail 100 <container>`            |
| **View logs from the last 15 minutes**               | `docker logs --since 15m <container>`           |
| **Filter logs with grep**                            | `docker logs <container> \| grep <filter>`      |
| **View logs for a Docker Compose service**           | `docker compose logs <service>`                 |
| **Follow logs for all Compose services**             | `docker compose logs -f`                        |
| **Remove the service prefix in Docker Compose logs** | `docker compose logs --no-log-prefix <service>` |

Docker is designed to capture the standard output ( stdout ) and standard error
( stderr ) streams from the main process running inside a container.

This means that if you are containerizing your own services, you should ensure
that they're writing their logs to stdout or stderr so that Docker’s built-in
logging system can capture them.

A logging driver acts as the backend for these logs. It receives the log streams
from the container and determines whether to store them in a file or forward
them to an endpoint.

Here's the typical Docker logs location for a container:
`/var/lib/docker/containers/<container-id>/<container-id>-json.log`

Find it in bash using:
`docker inspect -f '{{.LogPath}}' <container_name_or_id>`