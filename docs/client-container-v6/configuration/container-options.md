# Advanced Docker Configuration

**This page describes advanced aspects of Docker Container configuration. Most of these options are rarely needed.**



## Timezone
The default timezone in the sedex Client Docker container is "Europe/Zurich" (i.e. local Swiss time).
If you have to set another timezone (e.g. UTC) then set the environment variable **TZ** by adding the following option to your container run statement:
```
--env TZ=UTC   
```



## Running the sedex Client as a non-root user
Non-root container images add an extra layer of security and are generally recommended for production environments. The following steps describe how to set up a non-root group and user in case the host environment does not already provide these.

##### Step 1: Create a non-root group on the host machine
Determine a group ID (501 in the this example) that is unique on the host machine and create the group (the name does not have to be "sedex-group").
```console
$ groupadd --gid 501 sedex-group
```

##### Step 2: Create a non-root user on the host machine
Determine a user ID (901 in the this example) that is unique on the host machine and create the user (the name does not have to be "sedex-user"), assign it to the group created in the previous step, and specifying its home directory.
```console
$ useradd --uid 901 --no-log-init --gid sedex-group --create-home --home-dir /opt/sedexclient/ sedex-user
```

##### Step 3: Set the group and owner of the sedex-data directories
If the sedex Client will run as a non-root user inside of the Docker container, set the group and owner of the whole sedex-data directory tree and its contents so that the non-root user can access them.
```console
$ chown --recursive sedex-user:sedex-group /path/to/sedex-data
```

##### Step 4: Run the sedex Client container as a non-root user
Run the sedex Client container using environment-specific values for the following options:

- `/path/to/sedex-data` - The path to the host's "sedex-data" directory (holding the persisted data)
- `YOUR_MONITORING_PORT` - The port at which the monitoring web page of the sedex Client should be accessible from the outside of the container
- `YOUR_WS_PROXY_HTTP_PORT` - The unsecured http port at which the sedex Clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used or will only be used via https.
- `YOUR_WS_PROXY_HTTPS_PORT` - The secured https port at which the sedex Clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used.
- `--stop-timeout 65` - When the docker container is stopped (e.g. with "docker stop" command), the sedex Client's controller-stop.sh is executed to initiate a graceful 
  shutdown of the client. This typically takes longer than the default 10 seconds that the Docker daemon waits before killing the container.
  The --stop-timeout option overrides the default. The required time will vary from one installation to another.
- `--restart unless-stopped` - Restart Policy: Restart the container automatically after Docker daemon restarts, unless the container has been stopped intentionally. 
- `--user` - User: Sets the UID and GID used to start the sedex Client.
- `-d` - Detached: Start container in background without showing log output on console.

<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->

To run the sedex Client container as a non root user enter the following commands:

```console
$ docker pull sedexch/sedex-client:latest

$ docker run \
  --name sedex Client \
  --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
  --publish YOUR_MONITORING_PORT:8000 \
  --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
  --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
  --stop-timeout 65 \
  --restart unless-stopped \
  --user 901:501 \
  -d \
  sedexch/sedex-client:latest
```


## Running the Container in Read-Only mode

The sedex Client Docker container can be started with a read-only root filesystem, which further increases the security of the container.

To run the sedex Client container with its root filesystem mounted as read-only enter the following commands:
```console
$ docker pull sedexch/sedex-client:latest

$ docker run \
  --name sedex Client \
  --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
  --publish YOUR_MONITORING_PORT:8000 \
  --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
  --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
  --stop-timeout 65 \
  --restart unless-stopped \
  --read-only \
  -d \
  sedexch/sedex-client:latest
```

The Docker option `--read-only` mounts the container’s root filesystem as read only prohibiting writes to locations other than the specified volumes for the container.


## Running a specific version of the container
If your are referencing the container by its name "sedexch/sedex-client" the version with the tag "latest" is automatically selected.
To use a specific sedex Client version and/or or a specific container version, use a versioned tag explicitly.
View the [list of available versions](https://hub.docker.com/r/sedexch/sedex-client/tags) in the  Docker hub registry.

**Note:** For simplicity the run parameters are ommited in the following examples.
These parameters have to be set as described in the run example above.

Example: Run the latest Docker container of the latest sedex Client:
```console
$ docker run sedexch/sedex-client
```

or

```console
$ docker run sedexch/sedex-client:latest
```

Example: Run the latest Docker container of the sedex Client 6.0.0:
```console
$ docker run sedexch/sedex-client:6.0.0
```

Example: Run the Docker container 0.95-beta of the sedex Client 6.0.0:
```console
$ docker run sedexch/sedex-client:6.0.0_container-0.95-beta
```

## Pulling a specific image version
The "docker run" command does **not** automatically download a newer image from docker hub, if an older image with that name and tag is available locally.
In this case you have to manually issue a "docker pull" command, to download the newest images from docher hub.

Example: Get the latest Docker container of the latest sedex Client:
```console
$ docker pull sedexch/sedex-client
```

Example: Get the latest Docker container of the sedex Client 5.3.1:
```console
$ docker pull sedexch/sedex-client:6.0.0
```

Example: Get the Docker container 0.95-beta of the sedex Client 6.0.0:
```console
$ docker pull sedexch/sedex-client:6.0.0_container-0.95-beta
```

## Separate permanent stores for conf, log, etc.  
For the permanent store of data outside the container, most users will be happy to use one single directory "sedex-data" that holds all the different data types in one place:

- configuration
- sedex messages
- messaging state
- log files
- etc.

There are good reasons to separate these stores, e.g. different users and permissions. So you can mount the different subdirectories seperately:
```console
$ mkdir /path/to/sedex-conf
$ mkdir /path/to/sedex-interface
$ mkdir /path/to/sedex-internal
$ mkdir /path/to/sedex-logs
$ mkdir /path/to/sedex-monitoring

$ docker run \
  --name sedex Client \
  --mount type=bind,source=/path/to/sedex-conf,destination=/sedex-data/conf/ \
  --mount type=bind,source=/path/to/sedex-interface,destination=/sedex-data/interface/ \
  --mount type=bind,source=/path/to/sedex-internal,destination=/sedex-data/internal/ \
  --mount type=bind,source=/path/to/sedex-logs,destination=/sedex-data/logs/ \
  --mount type=bind,source=/path/to/sedex-monitoring,destination=/sedex-data/monitoring/ \
  --publish YOUR_MONITORING_PORT:8000 \
  --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
  --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
  --stop-timeout 65 \
  --restart unless-stopped \
  -d \
  sedexch/sedex-client:latest
```
