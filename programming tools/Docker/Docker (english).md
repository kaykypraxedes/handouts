```
______               _                
|  _  \             | |               
| | | |  ___    ___ | | __  ___  _ __ 
| | | | / _ \  / __|| |/ / / _ \| '__|
| |/ / | (_) || (__ |   < |  __/| |   
|___/   \___/  \___||_|\_\ \___||_|   
```

**Author: Kayky Moreira Praxedes**

# 0. Basic Concepts

## Docker:

**Docker** allows you to prepare and run applications with the programs and libraries they need, in isolated environments called ***containers***. This reduces differences between computers: instead of installing and configuring each dependency directly on the system, an environment prepared for the application is used.

The computer that runs this environment is called the *host*. In the examples in this handbook, it is the same computer on which the commands are typed.

## Images and *Containers*:

An **image** contains the files and configurations used as a basis for running an application. A ***container*** is the environment created from that image, with its own processes, configurations, and file changes.

One image can give rise to several *containers*. Changing the files in one of them does not modify the original image or the files of the others, except when they use shared storage.

> For those familiar with object orientation, a possible comparison is to think of the image as a class and the *container* as an object created from it.

A *container* remains running while its **main process** is active. If that program ends, the *container* stops. Stopping does not mean removing: its files remain available for a new start. When the *container* is removed, data written only in it is deleted; to keep it regardless of the *container*, volumes or mounted directories are used (chapter 5).

Linux *containers* share the kernel of the Linux environment in which they run, instead of each one starting a complete operating system. In Docker Engine installed directly on Linux, that kernel is the host's; in Docker Desktop, Linux *containers* are normally inside a virtual machine managed by the tool.

## *Registries* and Image Names:

A ***registry*** is a service that stores and distributes images. **Docker Hub** is used by default, but private servers and servers from other providers also exist.

The reference `nginx:alpine` identifies the `nginx` image and the ***tag*** `alpine`, which selects a version or variant. A reference such as `registry.example.com/equipe/app:1.0` also informs the server and the repository organization.

If the *tag* is omitted, Docker uses `latest`. This name is a convention: it does not guarantee that it is the most recent version. *Tags* can start pointing to other content when an image is updated.

## Command Structure:

The form most used in this handbook is `docker {object} {command} {options} {arguments}`. The object indicates the resource, such as `image`, `container`, or `volume`; the command indicates the operation, such as `ls`, `run`, or `rm`.

> In the syntax models, **elements enclosed in braces indicate content to be replaced** (such as commands, paths, values, flags, etc.), and should be removed in actual use, unless they are explicitly presented as part of the syntax. `{options}` indicates optional modifiers; the main description presents the behavior without them, considering the indicated arguments. When an argument can also be omitted, this will be explained in the text. The concrete examples already present the form used in the terminal.

Some commands have short forms: `docker container run` is equivalent to `docker run`, `docker container ls` to `docker ps`, `docker image ls` to `docker images`, and `docker image rm` to `docker rmi`. Here, the extended form is kept to make explicit the resource used.

## Manuals:

- `docker --help` - Shows the main commands and global options.

- `docker {object} --help` - Shows the operations available for a resource, such as `docker image --help`.

- `docker {object} {command} --help` - Shows the help for an operation, such as `docker container run --help`.

- `docker compose {command} --help` - Shows the help for a Compose operation (chapter 7). Without the command, `docker compose --help` shows the general help.

---

# 1. Configuration and Permissions

## Installation and Verification:

The `docker` command uses a service, called **Docker Engine**, to manage images, *containers*, networks, and volumes. Having the command installed does not mean that this service is running.

Consult the official instructions to install [Docker Engine](https://docs.docker.com/engine/install/) or [Docker Desktop](https://docs.docker.com/desktop/). Desktop brings together the runtime environment, a graphical interface, and Compose; on Linux, Engine can also be installed directly, without Desktop.

- `docker version {options}` - Shows the client and server versions. If the client responds but the server part shows an error, check whether the service is available and whether your user can access it.

- `docker info {options}` - Shows information about the running environment, including the number of images and *containers*.

## Service on Linux:

The commands below apply to **Docker Engine installed as a system service on distributions with `systemd`**. In Docker Desktop, the environment is started by the application itself.

- `sudo systemctl start docker` - Starts the service now.

- `sudo systemctl enable docker` - Configures the service to start automatically with the system.

- `systemctl status docker` - Checks the service state.

- `sudo journalctl -u docker.service` - Checks the service logs. `-f` follows new messages and `-b` limits to the current boot.

## Running without `sudo`:

In the traditional Engine installation on Linux, access depends on permission to communicate with the service. To allow access by the `docker` group:

```bash
# Execute apenas se o grupo ainda não existir.
sudo groupadd docker

# Adicione seu usuário ao grupo.
sudo usermod -aG docker "$USER"
```

After that, just refresh the terminal.

> This does not exempt `sudo` for administrative operations such as starting the service.

**Joining the `docker` group grants access equivalent to that of an administrator on the computer.** This configuration should be limited to trusted users.

> *Rootless* mode is an alternative in which the service and *containers* run without administrative privileges. It has its own installation and configuration; consult the [official guide](https://docs.docker.com/engine/security/rootless/).

---

# 2. Images and *Registries*

## Consulting and Downloading Images:

- `docker image ls {options}` - Lists the images stored locally. `-q` shows only the identifiers.

- `docker image inspect {options} {images}` - Shows detailed information in JSON, such as configuration, architecture, and identifiers. `--format` allows selecting fields.

- `docker image pull {options} {image}` - Downloads an image from the indicated *registry* or, by default, from Docker Hub.

```bash
docker image pull nginx:alpine
docker image ls
```

Downloading an image does not start the application. It becomes available to create *containers*.

## Identifying and Publishing Images:

- `docker image tag {source} {destination}` - Creates another name or *tag* for the same local image, without duplicating its content.

- `docker login {options} {registry}` - Authenticates to the indicated server. Without the server, it uses Docker Hub. The authentication flow depends on the service.

    - `docker login -u {user} --password-stdin {registry}` - Receives the password or token through standard input, avoiding placing it directly in the command.

- `docker image push {options} {image}` - Sends the image to the *registry* and repository indicated in its name. You must have publishing permission on the destination.

- `docker logout {registry}` - Removes the local authentication for the indicated server; if omitted, it uses Docker Hub.

Assuming that `minha_aplicacao:1.0` already exists locally, publishing to Docker Hub can be done as follows, replacing `seu_usuario` with the authorized account:

```bash
docker image tag minha_aplicacao:1.0 seu_usuario/minha_aplicacao:1.0
docker login
docker image push seu_usuario/minha_aplicacao:1.0
```

Credentials are associated with the client configuration. Prefer integration with the system's credential vault; credentials merely encoded in `~/.docker/config.json` are not encrypted.

## Removing Images:

- `docker image rm {options} {images}` - Removes the indicated local references. Images used by *containers* may prevent the operation.

    - `docker image rm -f {images}` - Forces removal in the situations allowed by Docker; it does not replace the management of the *containers* that use the image.

An image can have several names and share parts of its content with other images. Therefore, removing a reference does not always free all the space shown for it.

## Transferring Images by File:

- `docker image save {options} {images}` - Generates a TAR file with the images, sending the content to standard output when no destination file is chosen.

    - `docker image save -o {file} {images}` - Writes the TAR to the indicated file.

- `docker image load {options}` - Loads images from a TAR received through standard input.

    - `docker image load -i {file}` - Reads the TAR from the indicated file.

```bash
docker image save -o nginx.tar nginx:alpine
docker image load -i nginx.tar
```

These commands transfer images. Data stored in *containers* and volumes are not included automatically.

---

# 3. *Containers*

## Creating and Running:

- `docker container create {options} {image}` - Creates a *container* without starting it.

- `docker container run {options} {image} {command} {arguments}` - Creates a new *container* and starts its main process. Downloads the image if it is not available locally. The command and its arguments can be omitted to use the default defined by the image.

    - `docker container run --name {name} {image}` - Assigns a name to make the next commands easier.
    - `docker container run -d {image}` - Runs in the background and releases the terminal.
    - `docker container run -it {image} {command}` - Keeps input open (`-i`) and provides a terminal (`-t`), allowing interaction with the program.
    - `docker container run --rm {image}` - Automatically removes the *container* when it ends.

Options can be combined:

```bash
docker container run --name servidor -d nginx:alpine
docker container run --rm -it alpine:3.22 sh
```

The first command keeps the server in the background, still without publishing a port for browser access. The second opens an `sh` terminal in another *container*; when you run `exit`, its main process ends and it is removed.

**Each `run` creates a new container.** To start a stopped one again, use `start`. Repeating `run` with the same `--name` causes a conflict as long as the previous *container* exists, even stopped.

## Checking State and Resources:

- `docker container ls {options}` - Lists the *containers* running. `-q` shows only the identifiers.

    - `docker container ls -a` - Includes stopped *containers*.

- `docker container inspect {options} {containers}` - Shows the configuration and state in JSON. `--format` allows selecting fields.

- `docker container stats {options} {containers}` - Monitors CPU, memory, network, and read/write usage. Without names, it monitors the running *containers*; `--no-stream` shows only one reading.

- `docker container top {container}` - Shows the processes of the *container*.

## Stopping, Resuming, and Removing:

- `docker container stop {options} {containers}` - Requests termination of the process and forces completion if it does not stop within the time limit. `-t {seconds}` adjusts this wait.

- `docker container start {options} {containers}` - Starts existing *containers* that are stopped, preserving their configuration.

    - `docker container start -ai {container}` - Starts and connects the terminal to the *container*'s input and output.

- `docker container restart {options} {containers}` - Stops and starts again. `-t {seconds}` adjusts the wait during stopping.

- `docker container kill {options} {containers}` - Forces immediate termination with `SIGKILL`. The `-s {signal}` option allows sending another signal.

- `docker container rm {options} {containers}` - Removes stopped *containers* and data written only in them.

    - `docker container rm -f {containers}` - Forces termination and removal of running *containers*.
    - `docker container rm -v {containers}` - Also removes associated anonymous volumes; it does not remove named volumes.

```bash
docker container stop servidor
docker container start servidor
docker container stop servidor
docker container rm servidor
```

The sequence stops, resumes, and finally removes the server from the previous example. The image remains available. Stopping preserves the *container*'s files; removing eliminates its writable layer.

## Checking *Logs* and Running Commands:

- `docker container logs {options} {container}` - Shows the application's recorded output. `--tail {quantity}` limits to the last lines and `-t` adds timestamps. Availability depends on the application's and Docker's logging configuration.

    - `docker container logs -f {container}` - Follows new messages. `Ctrl+C` ends following, without stopping the *container*.

- `docker container exec {options} {container} {command} {arguments}` - Runs another process in a *container* that is already running. The arguments depend on the program and can be omitted.

    - `docker container exec -it {container} sh` - Opens an interactive `sh` session, if that program exists in the *container*.

It is possible to use `bash` instead of `sh` when it is installed. In that case, `exit` ends the session created by `exec`, while the main process continues running.

- `docker container cp {source} {destination}` - Copies files between the computer and a *container*. Use `{container}:{path}` to indicate the *container* side.

```bash
docker container cp pagina.html servidor:/usr/share/nginx/html/index.html
docker container cp servidor:/etc/nginx/nginx.conf ./nginx.conf
```

The examples assume an Nginx *container* named `servidor`; the first line also requires the local file `pagina.html`.

## Configuring the Application:

- `docker container run -e {key}={value} {image}` - Defines an environment variable for the application.

- `docker container run --env-file {file} {image}` - Loads variables from a file with lines in the `KEY=value` format.

- `docker container run --restart {policy} {image}` - Defines when to automatically restart the *container*. `no` disables the policy; `on-failure` reacts to termination with an error; `unless-stopped` allows automatic resumptions, preserving the decision of a manual stop.

These settings are defined when the *container* is created. A `start` reuses the existing ones. Variables only have an effect if the application recognizes them, and they should not be treated as a vault of credentials.

> For specific needs, `--memory` and `--cpus` limit resources; `-u` chooses the process user; `--pull` controls when to obtain the image. Consult `docker container run --help` for the syntax and accepted values. Ports, networks, and storage are detailed in chapters 5 and 6.

---

# 4. *Dockerfile* and Image Building

## *Dockerfile*:

A ***Dockerfile*** is a text file with the instructions to build an image. It records how to prepare the files and the environment, allowing the build to be repeated without depending on manual changes in a *container*.

- `FROM {image}` - Chooses the base image.

- `WORKDIR {path}` - Defines the directory used by the following instructions and by the program run when starting the *container*.

- `COPY {source} {destination}` - Copies files from the build context to the image.

- `RUN {command}` - Runs an operation during the build, such as installing dependencies.

- `ENV {key}={value}` - Defines a variable in the image environment.

- `USER {user}` - Defines the user for the next `RUN` instructions and for running the *container*.

- `EXPOSE {port}` - Documents the port used by the application. It does not publish the port on the computer.

- `ENTRYPOINT ["{program}", "{argument}"]` - Defines the main program. The list can have only the program or include several arguments.

- `CMD ["{program}", "{argument}"]` - Defines the default command. When an `ENTRYPOINT` exists, the content of `CMD` normally provides its default arguments.

**`RUN` happens during the build; `CMD` and `ENTRYPOINT` define the container's execution.** When providing another command in `docker container run`, it replaces `CMD`; with an `ENTRYPOINT` in list form, that content is passed as an argument to the main program.

## Context and `.dockerignore`:

The **build context** gathers the files available to create the image. In `docker image build .`, the dot indicates the current folder. The source paths of `COPY` are relative to this context, not to any folder on the computer.

The `.dockerignore` file, placed at the root of the context, excludes files that do not need to be sent to the build:

```gitignore
.git/
.env
node_modules/
build/
*.log
```

This reduces the transferred content and avoids including unnecessary local files. Credentials should not be copied into the image or written in its instructions; when the build needs them, use [build secrets](https://docs.docker.com/build/building/secrets/).

## Building an Image:

- `docker image build {options} {context}` - Builds an image using, by default, the `Dockerfile` at the root of the context.

    - `docker image build -t {name}:{tag} {context}` - Assigns a name and a *tag* to the result.
    - `docker image build -f {file} {context}` - Chooses another Dockerfile.
    - `docker image build --no-cache {context}` - Redoes the steps without reusing the stored results of previous builds.
    - `docker image build --pull {context}` - Tries to obtain an updated version of the base image.

For a complete example, create a `site` folder, place in it an `index.html` file with the desired content, and save the following `Dockerfile` next to that folder:

```dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY site/ .
EXPOSE 8000
CMD ["python", "-m", "http.server", "8000", "--bind", "0.0.0.0"]
```

This example uses the HTTP server included in Python to serve the files from `/app`. In the folder containing the Dockerfile, build and run:

```bash
docker image build -t meu_site:1.0 .
docker container run --rm -d --name meu-site \
    -p 127.0.0.1:8000:8000 meu_site:1.0
```

Access `http://localhost:8000`. The `-p` option, detailed in chapter 6, allows access from the computer. If you change the local files, build the image again and replace the *container*: the files were copied during the build, not linked to the original folder. In this example, `docker container stop meu-site` also removes it because of `--rm`.

> `docker container commit {container} {image}` can capture changes made to a *container*'s files, but it does not document the steps that produced that state nor include volume data. To keep the build reproducible, prefer the Dockerfile.

---

# 5. Storage

## Choosing Where to Store Data:

Data written only in the *container* survives a stop, but is lost when it is removed. To separate the data from the *container*'s life, use a mount: a path inside it starts to access storage external to its writable layer.

| Type | When to Use |
| --- | --- |
| **Volume** | For persistent data managed by Docker, such as database files. |
| ***Bind mount*** | To share a specific file or directory from the computer, such as source code or configurations. |

> `tmpfs` offers temporary storage in memory, discarded when the *container* stops. It is an alternative for data that does not need to be persisted.

## Managing Volumes:

- `docker volume create {options} {volume}` - Creates a volume. If the name is omitted, Docker generates an identifier.

- `docker volume ls {options}` - Lists the existing volumes.

- `docker volume inspect {options} {volumes}` - Shows detailed information about the volumes.

- `docker volume rm {options} {volumes}` - Removes volumes that are not associated with *containers*, deleting their data.

Volumes with a chosen name, such as `dados`, are called **named**. Those created without a chosen name are called **anonymous**. This difference affects some removal operations, such as `container rm -v`.

## Associating a Volume:

- `docker container run --mount type=volume,src={volume},dst={destination} {image}` - Makes a volume available at the indicated path inside the *container*.

The example below writes a file to the volume and reads it using another *container*:

```bash
docker volume create dados
docker container run --rm --mount type=volume,src=dados,dst=/dados \
    alpine:3.22 sh -c 'echo "Teste de persistência" > /dados/teste.txt'
docker container run --rm --mount type=volume,src=dados,dst=/dados \
    alpine:3.22 cat /dados/teste.txt
```

Both *containers* are removed when they end, but the file remains in the `dados` volume. To remove it permanently, use `docker volume rm dados` when you no longer need its content.

## Mounting a Computer Directory:

- `docker container run --mount type=bind,src={source},dst={destination} {image}` - Associates a computer path with a path inside the *container*. In the form presented, the source must exist.

In Bash, while in the folder containing the `site` directory from the previous chapter:

```bash
docker container run --rm -d --name site-local \
    -p 127.0.0.1:8080:80 \
    --mount type=bind,src="$PWD/site",dst=/usr/share/nginx/html,readonly \
    nginx:alpine
```

Access `http://localhost:8080`. `$PWD` represents the current folder; `readonly` allows the *container* to read the files, without changing them through this mount. Edits made on the computer become available to the server without rebuilding the image.

The short form `-v {source}:{destination}` also accepts volumes and directories. A name such as `dados` indicates a volume; an absolute path indicates a computer directory. Adding `:ro` makes the mount read-only.

> On systems with SELinux, `:Z` and `:z` in the `-v` option adjust access labels for private use or sharing between *containers*. These adjustments modify the host files and should be limited to the necessary folder. Consult the [bind mounts documentation](https://docs.docker.com/engine/storage/bind-mounts/).

---

# 6. Networks and Ports

## Accessing the Application from the Computer:

The application can be running inside the *container* without being available in the computer's browser. To allow this access, publish its port when creating the *container*.

- `docker container run -p {host_port}:{container_port} {image}` - Forwards the computer port to the port used by the application in the *container*. Without an explicit IP, it normally publishes on all host interfaces.

- `docker container run -p 127.0.0.1:{host_port}:{container_port} {image}` - Limits publication to the computer itself.

- `docker container port {container}` - Shows the published ports of a *container*.

In `-p 127.0.0.1:8080:80`, the browser accesses port **8080 of the computer**, while the server uses port **80 in the container**. Another application cannot simultaneously occupy the same address and port on the computer.

TCP is the default protocol. For UDP, add `/udp`, as in `-p 5353:53/udp`. The `EXPOSE` instruction in the Dockerfile only documents the port; it does not replace `-p`.

## Communication between *Containers*:

A user-created *bridge* network allows connected *containers* to find each other by name. This avoids depending on IP addresses that can change.

- `docker network create {options} {network}` - Creates a network; the default type is *bridge*.

- `docker network ls {options}` - Lists the networks.

- `docker network inspect {options} {networks}` - Shows the configuration and the connected *containers*.

- `docker network connect {options} {network} {container}` - Connects an existing *container* to the network.

- `docker network disconnect {options} {network} {container}` - Disconnects a *container* from the network.

- `docker network rm {options} {networks}` - Removes networks that are not in use.

- `docker container run --network {network} {image}` - Connects the new *container* to the indicated network.

```bash
docker network create minha-rede
docker container run -d --name web --network minha-rede nginx:alpine
docker container run --rm --network minha-rede alpine:3.22 \
    wget -qO- http://web:80
```

The last command queries the server by the name `web`. It is not necessary to publish the port with `-p` for this internal communication. Inside a *container*, `localhost` points to itself, not to another *container* or to the computer.

---

# 7. *Docker Compose*

## Purpose and Syntax:

**Docker Compose** allows you to save an application's configuration in a file, instead of repeating long creation commands. It uses Docker Engine to create and manage the *containers*, networks, and volumes declared.

| Element | Function |
| --- | --- |
| Dockerfile | Describes how to build an image. |
| `docker container run` | Creates a *container* with the configuration informed in the terminal. |
| `compose.yaml` | Gathers the configuration of the services and resources of an application. |

- `docker compose version {options}` - Checks the Compose installation. It comes with Docker Desktop; in Engine installed directly on Linux, it may be necessary to install the [Compose plugin](https://docs.docker.com/compose/install/linux/).

The general syntax is `docker compose {global_options} {command} {options} {arguments}`. Global options appear before the operation; specific ones, after:

- `docker compose -f {file} {command}` - Chooses another Compose file.

- `docker compose -p {project} {command}` - Defines the project name, which groups the application's resources.

- `docker compose --env-file {file} {command}` - Chooses the variables file used to fill values in the Compose model.

In `docker compose -f compose.dev.yaml up -d`, `-f` is global and `-d` belongs to `up`.

## First Compose File:

Save the following content as `compose.yaml`, in a project folder:

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "127.0.0.1:8081:80"
```

`services` gathers the application's components. `web` is the name chosen for this service, `image` selects the image, and `ports` publishes the port. YAML indentation uses spaces and determines the relationship between the elements.

In the same folder, run:

```bash
docker compose up -d
docker compose ps
```

The default Nginx page will be available at `http://localhost:8081`. Compose creates a network for the project, and services on that network can communicate by their names. Compose commands receive **service** names, such as `web`, even when the generated name for the *container* is different.

## Building, Creating, and Updating:

In the commands below that accept `{services}`, omitting them selects all services applicable to the operation.

- `docker compose up {options} {services}` - Obtains or builds the images according to the configuration, creates or updates the *containers*, and starts them. Without options, it follows the output in the terminal; `Ctrl+C` interrupts execution and stops the started services.

    - `docker compose up -d {services}` - Starts in the background.
    - `docker compose up --build {services}` - Builds the images of the services with `build` before starting. Can be combined with `-d`.

- `docker compose create {options} {services}` - Creates the *containers* without starting them.

- `docker compose build {options} {services}` - Builds the images of the services that use `build`, without starting the *containers*.

- `docker compose pull {options} {services}` - Downloads the declared images, without replacing the running *containers* by itself.

To use a Dockerfile from the current folder, the service can declare `build: .` instead of `image`. Port publication must match the application: in the Python example from chapter 4, the internal port is 8000, not 80.

After modifying the code of a custom image, use `docker compose up -d --build`. To update a ready-made image, use `docker compose pull` and then `docker compose up -d`. Recreation preserves mounted volumes but discards data that existed only in the replaced *container*.

## Consulting and Running Commands:

- `docker compose config {options}` - Validates and shows the resulting configuration, including variable substitution.

- `docker compose ps {options} {services}` - Lists the project's active *containers*.

    - `docker compose ps -a {services}` - Includes stopped ones.

- `docker compose logs {options} {services}` - Shows the services' recorded output. `--tail {quantity}` limits to the last lines.

    - `docker compose logs -f {services}` - Follows new messages.

- `docker compose exec {options} {service} {command} {arguments}` - Runs a process in an active *container* of the service. The arguments can be omitted; the session is already interactive by default when run in a terminal.

- `docker compose run {options} {service} {command} {arguments}` - Creates a separate *container* for a one-off task. The command and its arguments can be omitted to use the service default.

    - `docker compose run --rm {service} {command} {arguments}` - Removes that task's *container* when it ends.

```bash
docker compose exec web sh
docker compose run --rm web nginx -t
```

The first line opens a terminal in the already active service. The second creates another *container* to check the Nginx configuration. `run` does not publish the service's declared ports by default; `--service-ports` enables this publication when necessary.

## Volumes, Variables, and Dependencies:

Other fields can be added to the service as the application needs:

| Field | Purpose |
| --- | --- |
| `volumes` | Associates volumes or computer directories. |
| `environment` | Defines variables delivered to the application. |
| `env_file` | Loads these variables from a file. |
| `command` and `entrypoint` | Replace the image's execution defaults. |
| `restart` | Defines the restart policy. |
| `depends_on` | Declares dependencies between services. |
| `healthcheck` | Defines a command to check whether the service is healthy. |
| `networks` | Selects specific networks instead of only the default network. |

As another example, save this `compose.yaml` in a separate project folder. It defines a Redis with a named volume and persistence enabled:

```yaml
services:
  cache:
    image: redis:alpine
    command: ["redis-server", "--appendonly", "yes"]
    volumes:
      - dados_cache:/data

volumes:
  dados_cache:
```

The volume is declared at the top level and associated with the service at `/data`. It can be reused when the *container* is recreated. The application also needs to be configured to write to this path.

In the first example, it is possible to replace the fixed publication with `"127.0.0.1:${APP_PORT:-8081}:80"`. The value of `APP_PORT` will be obtained from the terminal environment or from a `.env` file; if it is not defined or is empty, 8081 will be used.

```dotenv
APP_PORT=8082
```

**The `.env` fills values in the Compose file; it does not automatically deliver all variables to the containers.** To deliver them, use `environment` or `env_file`. Files with credentials should not be versioned.

> `depends_on` alone controls the start order, but does not guarantee that the dependent application is ready. When this wait is necessary, combine `healthcheck` in the depended-upon service with `condition: service_healthy` in its dependency. Consult the [startup order control](https://docs.docker.com/compose/how-tos/startup-order/).

## Stopping, Resuming, and Removing:

- `docker compose stop {options} {services}` - Stops the *containers* without removing them. `-t {seconds}` adjusts the wait.

- `docker compose start {options} {services}` - Resumes existing *containers* that are stopped; it does not create the missing ones.

- `docker compose restart {options} {services}` - Restarts the *containers*. It does not apply changes made to the Compose file; for that, use `up`.

- `docker compose down {options}` - Stops and removes the project's *containers* and networks, preserving named volumes by default.

    - `docker compose down -v` - Also removes declared named volumes and associated anonymous volumes. Resources declared as external are not removed.

**`down -v` can delete the application's persistent data.** To just interrupt the work and resume it later, use `stop` and `start`.

---

# 8. Maintenance and Security

## Checking and Freeing Space:

- `docker system df {options}` - Shows the space used by images, *containers*, volumes, and build results stored in *cache*. `-v` presents details.

- `docker system prune {options}` - Removes stopped *containers*, unused networks, images without references (*dangling*), and unused build *cache*. Requests confirmation.

    - `docker system prune -a` - Extends image cleanup to all those not associated with *containers*.
    - `docker system prune --volumes` - Includes unused anonymous volumes in the cleanup.

- `docker volume prune {options}` - Removes unused local anonymous volumes not used by any *container*. Requests confirmation.

    - `docker volume prune -a` - Also includes unused named volumes.

**"Unused" does not mean "unimportant."** A volume can contain necessary data even without being associated with a *container*. Check the scope before confirming; the `-f` option, available in these commands, eliminates confirmation.

## Checking Events:

- `docker system events {options}` - Follows events such as creation, start, stop, and removal of *containers*.

Use `logs` to check the application's output and `events` to investigate operations performed in the Docker environment. For problems starting the service itself, consult the `journalctl` presented in chapter 1.

## Access Precautions:

A *container*'s isolation depends on the permissions granted to it. `--privileged` and mounting `/var/run/docker.sock` can allow broad control over the computer; they should not be used as a generic solution for permission errors.

Mount only the necessary directories and use read-only when the application does not need to write. Before running third-party configurations, check mainly the mounts and privileges requested.

> When it is necessary to pin exactly the content of an image, use a *digest*, in the format `image@sha256:{value}`. Unlike a *tag*, it identifies a specific content.

---

# Sources

- DOCKER INC. *What is Docker?* Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/get-started/docker-overview/](https://docs.docker.com/get-started/docker-overview/). Accessed on: Sep. 15, 2026.
- DOCKER INC. *Rootless mode*. Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/engine/security/rootless/](https://docs.docker.com/engine/security/rootless/). Accessed on: Sep. 15, 2026.
- DOCKER INC. *docker*. Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/reference/cli/docker/](https://docs.docker.com/reference/cli/docker/). Accessed on: Sep. 15, 2026.
- DOCKER INC. *Dockerfile reference*. Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/reference/dockerfile/](https://docs.docker.com/reference/dockerfile/). Accessed on: Sep. 15, 2026.
- DOCKER INC. *Storage*. Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/engine/storage/](https://docs.docker.com/engine/storage/). Accessed on: Sep. 15, 2026.
- DOCKER INC. *Networking overview*. Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/engine/network/](https://docs.docker.com/engine/network/). Accessed on: Sep. 15, 2026.
- DOCKER INC. *Docker Compose*. Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/compose/](https://docs.docker.com/compose/). Accessed on: Sep. 15, 2026.
- DOCKER INC. *Set, use, and manage variables in a Compose file with interpolation*. Palo Alto: Docker Inc., 2026. Available at: [https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/](https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/). Accessed on: Sep. 15, 2026.
