# Run the sedex Client with Docker 

**We refer to Phase 2 as the normal usage of the sedex Client as container. The steps below show how to create, run, and stop the sedex client as a container on a Docker host.**



![Phase 2: Run the container](/assets/v6/phase-2.png)


!!! note "Initialized Permanent Data Storage required"

    The sedex client container can only be created for normal usage if an initialized permanent data storage ("sedex-data") exists. See [Phase 1: Initial Configuration](../../initial-configuration/initial_configuration_case_identification) for details on how to create the storage.


## Create and Start the sedex Client container (Run)

!!! info "docker run = docker create && docker start"

    With the Docker command `docker run` a new container is created and also started immediately. Thus, the run command corresponds to a composition of the commands `docker create` and `docker start`. Of course, you can use both ways to reach your goal.

Run the sedex Client container using environment-specific values for the following options:

- `/path/to/sedex-data` - The path to the host's "sedex-data" directory (holding the persisted data)
- `YOUR_MONITORING_PORT` - The port at which the monitoring web page of the sedex Client should be accessible from the outside of the container
- `YOUR_WS_PROXY_HTTP_PORT` - The unsecured http port at which the sedex Clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used or will only be used via https.
- `YOUR_WS_PROXY_HTTPS_PORT` - The secured https port at which the sedex Clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used.
- `--stop-timeout 65` - When the docker container is stopped (e.g. with `docker stop` command), the sedex Client's `controller-stop.sh` is executed to initiate a graceful shutdown of the client. This typically takes longer than the default 10 seconds that the Docker daemon waits before killing a container.
  The `--stop-timeout` option overrides the default. The required time will vary from one installation to another.
- `--restart unless-stopped` - Restart Policy: Restart the container automatically after Docker daemon restarts, unless the container has been stopped intentionally. 
- `-d` - Detached: Start container in background without showing log output on console.

<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->

To run the most current sedex Client container enter the following commands:
```console
$ docker pull sedexch/sedex Client:latest

$ docker run \
  --name sedex-client \
  --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
  --publish YOUR_MONITORING_PORT:8000 \
  --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
  --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
  --stop-timeout 65 \
  --restart unless-stopped \
  -d \
  sedexch/sedex-client:latest
```

!!! info "Info: latest vs. specific version of the container image"
    The instructions show how to start the most recent container image called *"latest"*.
    If you want to use a *specific* version of the container then see [Configuration - Container Options](../../configuration/container-options) for details.

!!! info "Info: sedex Client configuration"
    - After the very first start of the sedex Client container, a general default configuration is created. You find this configuration in the *central configuration file* of the sedex Client which is stored in the permanent data storage (`sedex-data/conf/sedex Client-configuration.properties`). 
    - If no such configuration file has been created after the very first start of the sedex Client, you can search for the cause in the Docker log file:
    ```console
    $ docker logs sedex-client
    ```
    - If specific configuration options are required, these can either be set in the central configuration file or as environment variables of the container. For details see section [Configuration - sedex Client Options](../../configuration/sedex-client-options).

    

#### Stop and start the sedex Client container

Stop the sedex Client container:
```console
$ docker stop sedex-client
```

Start the sedex Client container:
```console
$ docker start sedex-client
```

