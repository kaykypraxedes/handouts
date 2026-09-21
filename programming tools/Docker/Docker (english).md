```
______               _                
|  _  \             | |               
| | | |  ___    ___ | | __  ___  _ __ 
| | | | / _ \  / __|| |/ / / _ \| '__|
| |/ / | (_) || (__ |   < |  __/| |   
|___/   \___/  \___||_|\_\ \___||_|   
```

# 00. Basic Concepts

**Docker** is a platform for building, distributing, and running applications in isolated environments (**containers**). The application is packaged with its dependencies and basic configurations, reducing differences between development, testing, and runtime environments.

## Containers:

A container is not a complete virtual machine. On Linux, container processes share the host kernel, but have an isolated view of resources such as processes, networks, the file system, and users. This separation uses kernel mechanisms, mainly namespaces and cgroups, which generally makes containers lighter than traditional virtual machines.

Isolation is not absolute. Containers still use the host kernel, and options such as `--privileged`, mounting sensitive directories, and access to the Docker socket can grant extensive control over the system.

## Architecture:

Docker Engine uses a client-server architecture. The **client** `docker` interprets commands and sends requests through the Docker API. The **daemon** `dockerd` receives these requests and manages objects such as images, containers, networks, and volumes. The client and daemon can be on the same machine or on different machines.

In a traditional Linux installation, the client and daemon normally communicate through the Unix socket `/var/run/docker.sock`. Therefore, running `docker container run` does not mean the CLI itself created the container: it requested the operation from the daemon.

A **registry** stores and distributes images. Docker Hub is the registry used by default when the image reference does not specify another server, but private registries or registries from other providers can also be used.

## Images:

An **image** is an immutable template, normally made up of layers, that contains the file system and configurations required to start an application. The same image can produce several independent containers.

A **container** is a runnable instance of an image. When it is created, Docker adds a writable layer and associates configurations such as the main command, environment variables, volumes, networks, and ports. Changing or removing a container does not modify the image from which it was created.

> An interesting analogy is to think of the **image as a class** and the **container as an object instantiated from that class**.

The container remains running while its main process is active. This process has PID 1 inside the container and receives stop signals sent by Docker. When it exits, the container changes to the stopped state, even though its writable layer continues to exist.

By default, data written only to the container layer belongs to its lifecycle. Removing the container also removes this layer. Data that needs to survive container replacement must be stored in volumes or in mounted host directories.

### Image references:

An image can be identified approximately by `{registry}/{namespace}/{repository}:{tag}` or by a digest. Omitted parts receive default values. For example, `ubuntu:24.04` uses Docker Hub, while `registry.example.com/team/app:1.0` explicitly specifies the server.

When the tag is omitted, Docker uses `latest`. This name is only a conventional, mutable tag; it does not guarantee that the image is the most recent version chronologically.

A digest, such as `image@sha256:{value}`, identifies specific content.

## Command structure:

The canonical CLI form generally follows the pattern `docker {object} {command} {options} {arguments}`.
> Some commands also have shorter historical aliases. For example, `docker image ls` can be written as `docker images`, `docker container ls` as `docker ps`, `docker image rm` as `docker rmi`, and `docker container rm` as `docker rm`.

## Manuals:

- `docker --help` - Shows the main commands and global options.

- `docker {object} --help` - Shows the operations available for an object, such as `docker image --help`.

- `docker {object} {command} --help` - Shows the syntax, options, and examples for an operation, such as `docker container run --help`.

- `docker compose --help` - Shows Docker Compose commands and global options.

- `docker compose {command} --help` - Shows the documentation for a specific Compose operation.

- `docker version {options}` - Shows client and server versions and information.

- `docker info {options}` - Shows the Docker Engine configuration and general state.

You can also consult the [Docker CLI Reference](https://docs.docker.com/reference/cli/docker/), [Dockerfile Reference](https://docs.docker.com/reference/dockerfile/), [Docker Compose CLI Reference](https://docs.docker.com/reference/cli/docker/compose/), and [Compose File Reference](https://docs.docker.com/reference/compose-file/).

---

# 01. Configuration and Permissions

## Docker service:

On distributions that use `systemd`, Docker Engine runs as a service. Depending on the distribution and installation method, it may need to be started or enabled during system startup.

- `systemctl {action} docker.service` - Manages the Docker service.

- `journalctl {options} -u docker.service` - Queries the service logs. `-f` follows new messages, `-b` limits output to the current boot, and `--since` defines the period.

Service management remains an administrative operation and normally requires `sudo`, even after the user is granted access to the Docker CLI.

## Running without `sudo`:

The need for `sudo` comes from permission on the Docker daemon (accessible by `root` and the `docker` group), not from permission on `/var/lib/docker/` (where containers and images are stored). To allow the current user to access the daemon without `sudo`:

```bash
$ sudo groupadd docker  # required only if the group does not exist yet (you can check with "cat /etc/group | grep docker")
$ sudo usermod -aG docker "$USER" # adds the current user to the docker group (execution permission)
```

> The `docker` group grants privileges equivalent to those of `root`; only trusted users should belong to this group.

### Rootless Mode:

In **Rootless Mode**, both the daemon and containers run as a user without administrative privileges inside a user namespace. This model reduces the impact of vulnerabilities in the daemon or runtime and avoids granting access equivalent to `root` through the `docker` group.

Rootless mode has its own installation, socket, context, and data directory, as well as UID and GID mapping requirements. Therefore, it should be treated as a complete alternative configuration and not casually combined with the traditional daemon.

---

# 02. Images and Registries

## Information:

- `docker image ls {options} [{repository}[:{tag}]]` - Lists images stored locally. `-a` includes intermediate images, `-q` shows only identifiers, `--filter` filters, and `--format` customizes the output.

- `docker image inspect {options} {images}` - Shows detailed information in JSON, such as identifiers, tags, layers, architecture, and configuration. `--format` extracts specific fields.

- `docker image history {options} {image}` - Shows the layers and commands that formed an image.

## Transfer:

- `docker image pull {options} {name}[:{tag}|@{digest}]` - Downloads an image from a registry. `-a` downloads all tags, and `--platform` selects the platform.

- `docker image tag {source_image} {target_image}` - Creates another reference to the same image, normally before pushing it to a registry.

- `docker image push {options} {name}[:{tag}]` - Pushes an image to the registry indicated by its name. `-a` pushes all tags, and `--platform` selects a specific platform.

- `docker login {options} [{registry}]` - Authenticates the client with a registry. `-u` specifies the user, and `--password-stdin` receives the credential through standard input without placing it directly in the shell history.

- `docker logout [{registry}]` - Removes the authentication stored by the client for the specified registry.

The user must have permission in the target namespace before running `push`. Client credentials are normally associated with the configuration in `~/.docker/config.json`; when possible, a system credential store should be used instead of keeping credentials encoded directly in this file.

## Image files:

- `docker image save {options} {images}` - Saves one or more images, including layers and references, to a TAR file. `-o {file}` defines the output file.

- `docker image load {options}` - Loads images and tags from a TAR file created by `save`. `-i {file}` defines the input file; without it, the command reads standard input.

```bash
$ docker image save -o image.tar application:1.0
$ docker image load -i image.tar
```

`save` and `load` operate on images. In contrast, `docker container export` and `docker image import` transfer only a representation of the container's file system, without preserving the history, layers, and complete image configuration in the same way.

## Removal:

- `docker image rm {options} {images}` - Removes local image references. `-f` forces the operation, and `--no-prune` prevents the removal of untagged parent layers.

An image can have multiple tags or share layers with other images. Therefore, removing a reference does not guarantee that all corresponding layers are immediately freed. Existing containers can also prevent or change the effect of the removal.

---

# 03. Containers

## Creation and execution:

- `docker container create {options} {image} [{command} {arguments}]` - Creates a container without starting it.

- `docker container run {options} {image} [{command} {arguments}]` - Downloads the image if necessary, creates a new container, and starts its main process.

Some of the most important `run` options are:

- `-d` or `--detach` - Runs in the background.
- `-i` or `--interactive` - Keeps standard input open.
- `-t` or `--tty` - Creates a pseudo-terminal; it is often combined with `-i` as `-it`.
- `--name {name}` - Assigns a name to the container.
- `--rm` - Automatically removes the container when it exits.
- `-p {host}:{container}` - Publishes a container port on the host.
- `-e {key}={value}` - Defines an environment variable.
- `--env-file {file}` - Loads environment variables from a file.
- `--mount {configuration}` - Attaches a volume, bind mount, or `tmpfs`.
- `--network {network}` - Connects the container to a network.
- `--restart {policy}` - Defines a restart policy, such as `no`, `on-failure`, `always`, or `unless-stopped`.
- `--memory {limit}` and `--cpus {limit}` - Limit memory and CPU.
- `-u {user}[:{group}]` - Defines the user for the main process.
- `--pull={policy}` - Controls image retrieval: `missing`, `always`, or `never`.

Example:

```bash
$ docker container run -d \
    --name server \
    -p 127.0.0.1:8080:80 \
    --restart unless-stopped \
    nginx:alpine
```

Running `run` again creates another container, even when the same image is used. To restart a stopped container while preserving its configuration and writable layer, use `start`.

## Listing and information:

- `docker container ls {options}` - Lists containers. With no options, it shows only running containers. `-a` includes stopped containers, and `-q` shows only identifiers.

- `docker container inspect {options} {containers}` - Shows the configuration and state of one or more containers in JSON. `--format` selects fields.

- `docker container stats {options} [containers]` - Shows CPU, memory, network, and I/O usage. `--no-stream` produces only one reading.

- `docker container top {container} {ps_options}` - Shows the container's processes.

- `docker container port {container} [{port}]` - Shows published ports.

## Lifecycle:

- `docker container start {options} {containers}` - Starts stopped containers. `-a` attaches output, and `-i` attaches input.

- `docker container stop {options} {containers}` - Requests a graceful shutdown and forces termination after the timeout. `-s` selects the initial signal, and `-t` defines the waiting time.

- `docker container kill {options} {containers}` - Immediately sends a signal; the default is `SIGKILL`. `-s` selects another signal.

- `docker container restart {options} {containers}` - Stops and starts the containers again. `-s` and `-t` control the stop.

- `docker container rm {options} {containers}` - Removes containers. `-f` forces removal of a running container, and `-v` removes associated anonymous volumes.

Stopping a container does not remove it. Its configuration and writable layer remain available to `start`. Removal deletes this layer, but does not automatically delete the image or named volumes.

## Logs and command execution:

- `docker container logs {options} {container}` - Shows the captured output of the container process according to the logging driver. `-f` follows new messages, `--tail` limits the last lines, `--since` and `--until` define the period, and `-t` shows timestamps.

- `docker container exec {options} {container} {command} {arguments}` - Starts a new process inside a running container. `-it` creates an interactive session, `-u` defines the user, `-e` adds variables, and `-w` defines the working directory.

An interactive session can be started with:

```bash
$ docker container exec -it my_container sh
```

`bash` can be used when it is installed in the image. The command does not literally “enter” the container: it creates a new process while the container's main process remains active. Running `exit` ends only this shell started by `exec` and normally does not stop the container.

- `docker container cp {source} {destination}` - Copies files between the host and a container, using `{container}:{path}` on one side.

---

# 04. Dockerfile and Image Building

## Dockerfile:

A **Dockerfile** is a text file that reproducibly describes how to build an image (in practice, **an instruction manual for creating an image**). Its instructions are processed in order and generally produce reusable layers.

The main instructions are:

- `FROM {image}` - Defines the base image and starts a build stage.
- `WORKDIR {path}` - Defines the directory used by the following instructions and the default process.
- `COPY {source} {destination}` - Copies files from the build context into the image.
- `RUN {command}` - Executes an operation during the image build.
- `ENV {key}={value}` - Defines a persistent variable in the image configuration.
- `USER {user}` - Defines the user for the following instructions and the default process.
- `EXPOSE {port}` - Documents the port expected by the application; it does not publish it on the host.
- `ENTRYPOINT [...]` - Defines the main executable.
- `CMD [...]` - Defines the default command or arguments, which can be overridden at runtime.

Example:

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000
CMD ["python", "app.py"]
```

The JSON form of `CMD` and `ENTRYPOINT`, such as `CMD ["program", "argument"]`, executes the program directly and makes it easier for the main process to handle signals correctly.

## Context and `.dockerignore`:

The **build context** is the set of files made available to the builder. In `docker image build .`, the dot indicates the current directory as the context. The Dockerfile cannot arbitrarily copy files located outside it.

The `.dockerignore` file excludes items from the context, reducing transfers, cache invalidations, and the risk of adding unwanted data to the image:

```gitignore
.git/
.env
node_modules/
build/
*.log
```

Secrets should not be written to `ENV`, `ARG`, `COPY`, or intermediate layers, because they can remain in the image or its history. Builds that need to access a credential should use the BuildKit secrets mechanism.

## Building:

- `docker image build {options} {context}` - Builds an image from a Dockerfile. `-t {name}:{tag}` assigns a reference, `-f {file}` selects another Dockerfile, `--no-cache` ignores the cache, `--pull` attempts to update the base image, and `--build-arg` provides build arguments.

Example:

```bash
$ docker image build -t my_application:1.0 .
```

## Capturing a container:

- `docker container commit {options} {container} [{repository}[:{tag}]]` - Creates an image from the changes in a container's writable layer.

`commit` can be useful for investigation or a one-time capture, but it does not replace a Dockerfile: the sequence that produced the state is not documented and cannot be reproduced safely. Changes stored in mounted volumes are not included in the created image.

---

# 05. Storage

## Storage types:

Docker provides three main ways to make additional storage available to a container:

- **Volume:** a persistent area managed by Docker and independent of the container lifecycle. It is normally the preferred option for databases and application data.
- **Bind mount:** an existing file or directory on the host mounted directly in the container. It is useful for source code and configurations, but couples the container to the host structure.
- **`tmpfs`:** temporary storage kept in the host memory and removed when the container stops.

## Volumes:

- `docker volume create {options} [{volume}]` - Creates a volume.

- `docker volume ls {options}` - Lists volumes.

- `docker volume inspect {options} {volumes}` - Shows volume details.

- `docker volume rm {options} {volumes}` - Removes unused volumes. `-f` forces the request, but volumes in use by containers cannot be removed.

- `docker volume prune {options}` - By default, removes unused local anonymous volumes. `-a` includes unused named volumes, `--filter` limits the scope, and `-f` skips confirmation.

A volume can be attached when creating the container with:

```bash
$ docker container run --mount \
    type=volume,src=data,dst=/var/lib/application \
    image:tag
```

## Bind mounts:

A bind mount can be created with:

```bash
$ docker container run --mount \
    type=bind,src="$PWD/config",dst=/app/config,readonly \
    image:tag
```

Read-only mode reduces the risk of the container changing the host. Even so, processes with access to the mount can read the files it contains; directories containing keys, credentials, or personal data should not be mounted unless necessary.

The short option `-v` or `--volume` accepts the format `{source}:{destination}:{options}`. On systems with SELinux, such as Fedora, `:Z` creates a private label for one container, and `:z` creates a label that can be shared among containers:

```bash
$ docker container run -v "$PWD/data:/app/data:Z" image:tag
```

Relabeling changes the SELinux labels of the host files and should be applied only to the required directory.

---

# 06. Networks and Ports

## Networks:

By default, containers receive a virtual interface and connect to a network. The default `bridge` network provides basic connectivity, but user-created bridge networks provide better isolation and name resolution between containers.

Containers connected to the same custom network can normally use their names as hostnames, avoiding reliance on IP addresses that can change.

- `docker network create {options} {network}` - Creates a network. `-d` selects the driver, `--subnet` defines the subnet, and `--internal` restricts external access.

- `docker network ls {options}` - Lists networks.

- `docker network inspect {options} {networks}` - Shows the configuration and connected containers.

- `docker network connect {options} {network} {container}` - Connects an existing container to a network.

- `docker network disconnect {options} {network} {container}` - Disconnects a container.

- `docker network rm {networks}` - Removes networks that are not in use.

## Publishing ports:

Publishing a port creates forwarding from the host to the container. The general form is `{host_IP}:{host_port}:{container_port}/{protocol}`.

- `-p 8080:80` - Publishes container TCP port 80 on port 8080 of the host interfaces.
- `-p 127.0.0.1:8080:80` - Limits access to the local machine.
- `-p 5353:53/udp` - Publishes a port using UDP.

If the host address is omitted, the port is normally bound to all interfaces and may be accessible from other machines. For services intended only for local development, explicitly specify `127.0.0.1`.

`EXPOSE` in the Dockerfile documents the port used by the application, but does not create this publication. The `-p` option or an equivalent Compose declaration is required to access it through the host.

---

# 07. Docker Compose

## Compose:

**Docker Compose** is a declarative tool for defining and running applications made up of one or more containers. Instead of maintaining several `docker container run`, `docker network create`, and `docker volume create` commands, the application configuration is recorded in a `YAML` file and applied as a set.

Compose does not replace Docker Engine. It works as another API client and asks the same daemon to create images, containers, networks, and volumes. Nor is it, by itself, a cluster orchestrator equivalent to Kubernetes or Docker Swarm; its primary focus is managing an application on one Docker Engine or context.

The responsibilities can be summarized as follows:

| Element | Responsibility |
| --- | --- |
| Dockerfile | Describe how to build an image. |
| `docker container run` | Imperatively create and configure a container. |
| `compose.yaml` | Declare an application's services, networks, and volumes. |
| Docker Engine | Actually build and run the requested objects. |

## Installation and syntax:

Current installations use the plugin accessed through `docker compose`.

- `docker compose version {options}` - Shows the installed Compose version.

On Linux, the plugin is normally provided by the `docker-compose-plugin` package from Docker's official repositories. Docker Desktop already includes Compose.

## Compose file:

The preferred name is `compose.yaml` or `compose.yml`.

A **Compose project** groups an application's resources. Its name can be defined by `name`, by the global `-p` option, or derived from the directory, and is used as a prefix and label for the containers, networks, and volumes created.

The main file elements are:

- `services` - Defines the application's executable components.
- `image` - Selects an existing image.
- `build` - Defines the context and options for building an image.
- `command` and `entrypoint` - Override the image command or entry point.
- `ports` - Publishes ports on the host.
- `volumes` - Attaches volumes or bind mounts.
- `environment` - Defines variables in the container environment.
- `env_file` - Loads variables that will be passed to the container.
- `depends_on` - Declares dependencies and controls startup order.
- `healthcheck` - Defines how to check the service's health or readiness.
- `networks` - Connects services to specific networks.
- `restart` - Defines the restart policy.

Example:

```yaml
name: example

services:
  application:
    build: .
    ports:
      - "127.0.0.1:${APP_PORT:-8000}:8000"
    environment:
      REDIS_HOST: cache
    depends_on:
      cache:
        condition: service_healthy
    restart: unless-stopped

  cache:
    image: redis:alpine
    volumes:
      - cache_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5

volumes:
  cache_data:
```

Compose creates a default network for the project. In the example, `application` can access the `cache` service using `cache` as its hostname. The `cache_data` volume is declared at the top level and remains available when the containers are replaced.

In its simple form, `depends_on` controls startup order, but does not guarantee that the dependent program is ready to accept connections. Combining `healthcheck` with `condition: service_healthy` allows Compose to wait for the declared healthy state.

## Variables:

Compose can replace expressions such as `${VARIABLE}` with values from the shell environment or a `.env` file. Forms such as `${VARIABLE:-default}` and `${VARIABLE:?message}` are also accepted; they respectively define a default and require a value.

The `.env` file is used mainly to **interpolate the Compose model**. Its variables are not automatically passed to all containers: for that, they must be declared in `environment` or loaded through `env_file`.

```dotenv
APP_PORT=8000
```

`.env` and `env_file` files are text files, not secret vaults. They should have appropriate permissions and should not be versioned when they contain credentials. Substitution can be checked with `docker compose config`.

## Global options:

The general form is `docker compose {options} {command} {arguments}`. Some important global options are:

- `-f {file}` - Selects a Compose file; it can be repeated to combine files.
- `-p {project}` - Defines the project name.
- `--env-file {file}` - Selects the file used for interpolation.
- `--profile {profile}` - Enables an optional profile.

Global options must appear before the subcommand, as in `docker compose -f compose.dev.yaml up`.

## Creation and updates:

- `docker compose up {options} [services]` - Builds when necessary, creates or recreates, and starts the services. Without `-d`, it aggregates the logs and keeps the terminal attached.

- `docker compose create {options} [services]` - Creates the containers without starting them.

- `docker compose build {options} [services]` - Builds the images for services that use `build`.

- `docker compose pull {options} [services]` - Downloads the declared images.

When a service's configuration or image changes, `up` can replace its container while preserving mounted volumes. Changes made only in the old container's writable layer may be lost; the application must be rebuildable from the declared images, configurations, and volumes.

## Information and commands:

- `docker compose config {options} [services]` - Validates, combines, and shows the resolved model.

- `docker compose ps {options} [services]` - Lists the project's containers.

- `docker compose logs {options} [services]` - Shows service logs.

- `docker compose exec {options} {service} {command} {arguments}` - Runs a process in an active service container.

- `docker compose run {options} {service} {command} {arguments}` - Creates a separate container for a one-time task based on the service.

`exec` uses a container that is already running. In contrast, `run` creates a temporary container based on the service configuration, which is useful for tests, migrations, and administrative tasks.

## Stopping and removal:

- `docker compose stop {options} [services]` - Stops containers without removing them. The `-t` option defines the waiting time.

- `docker compose start [services]` - Starts existing containers; it does not create those that do not exist yet.

- `docker compose restart {options} [services]` - Restarts containers. The `-t` option defines the waiting time.

- `docker compose down {options} [services]` - Stops and removes containers and networks created for the project. `-v` removes declared named volumes and associated anonymous volumes, `--rmi` removes images, and `--remove-orphans` removes containers for services that are no longer in the file.

By default, `down` does not remove named volumes or resources declared as external. `docker compose down -v` removes the project volumes and can permanently delete databases and other persistent data.

---

# 08. Maintenance and Security

## Space usage:

- `docker system df {options}` - Shows the space used by images, containers, local volumes, and the build cache. `-v` shows details.

- `docker system prune {options}` - Removes unused resources, such as stopped containers, unused networks, dangling images, and the build cache. `-a` extends removal to images not used by containers, `--volumes` includes anonymous volumes, `--filter` restricts the scope, and `-f` skips confirmation.

`prune` commands determine their scope based on currently referenced resources, not on the importance of their data. Review the displayed list and options before confirmation, especially when including volumes.

## Events:

- `docker system events {options}` - Follows events produced by the daemon, such as container creation, start, stop, and removal.

This command differs from `docker container logs`: `events` shows daemon events, while `logs` retrieves the output of the process running in the container.

## Security precautions:

- `--privileged` grants the container very broad access to host devices and resources and should not be used as a general solution for permission errors.

- Mounting `/var/run/docker.sock` inside a container gives it control over the daemon and, in the traditional configuration, privileges equivalent to `root` on the host.

- Bind mounts allow the container to read or modify real host paths; they should be limited to what is necessary and, when possible, mounted as read-only.

- Third-party images, Dockerfiles, and Compose files should be examined before execution, because they can request dangerous mounts, devices, networks, and privileges.

- Image tags are mutable. For environments that require strict repeatability, versions should be pinned and, when necessary, validated by digest.

---

# Sources:

- DOCKER INC. *Docker Docs: Docker overview*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/get-started/docker-overview/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Docker Engine: Linux post-installation steps*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/engine/install/linux-postinstall/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Docker Engine security: Rootless mode*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/engine/security/rootless/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Docker CLI reference*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/reference/cli/docker/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Dockerfile reference*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/reference/dockerfile/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Storage*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/engine/storage/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Networking overview*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/engine/network/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Docker Compose*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/compose/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *How Compose works*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/compose/intro/compose-application-model/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Compose file reference*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/reference/compose-file/>. Accessed on: Sep. 15, 2026.

- DOCKER INC. *Set, use, and manage variables in a Compose file with interpolation*. [N.p.]: Docker Inc., [n.d.]. Available at: <https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/>. Accessed on: Sep. 15, 2026.
