# Start and Stop the sedex Client

**The following steps show how to create, start and stop a Docker container running the sedex Client.**

!!! info "Info: latest vs. specific version of the container image"
    The instructions show how to start the most recent container image called *"lastest"*.
    If you want to use a specific version of the container then see [Configuration - Container Options](../../configuration/container-options) for details.



## Create and Start the sedex Client container (Run)

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
- `-d` - Detached: Start container in background without showing log output on console.

<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->

To run the sedex Client container enter the following commands:
```console
$ docker pull sedexch/sedex Client:latest

$ docker run \
  --name sedex Client \
  --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
  --publish YOUR_MONITORING_PORT:8000 \
  --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
  --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
  --stop-timeout 65 \
  --restart unless-stopped \
  -d \
  sedexch/sedex Client:latest
```


**Note:** After the very first start of the sedex Client Docker container, a general default configuration is created.
If specific configuration options are required, these can be set in the central configuration file of the sedex Client.
With a text editor (e.g. "nano") open the central sedex Client configuration file named "sedex Client-configuration.properties" in the subdirectory "sedex-data/conf" and edit the contained configuration parameters as needed and as described in [Configuration - sedex Client Options](../../configuration/sedex Client-options):
```console
$ nano /path/to/sedex-data/conf/sedex Client-configuration.properties
```

If no such configuration file has been created after the very first start of the sedex Client, you can search for the cause in the Docker log file:
```console
$ docker logs sedex Client
```
    

#### Stop and start the sedex Client container

Stop the sedex Client container:
```console
$ docker stop sedex Client
```

Start the sedex Client container:
```console
$ docker start sedex Client
```
