# Advanced Docker Options

## Defining an outgoing HTTP-proxy in the init.conf

If an HTTP proxy is required to access the Internet, such a proxy server can optionally be entered in the initial configuration file init.conf.

Example:

    File: /path/to/sedex-data/conf/init.conf
    ...
    OUTGOING_WEB_PROXY_HOST=proxy.example.ch
    OUTGOING_WEB_PROXY_PORT=8080
    OUTGOING_WEB_PROXY_USER=example-user
    OUTGOING_WEB_PROXY_PASSWORD=1234
    
**Note:** If no user name is required, the two parameters OUTGOING_WEB_PROXY_USER and OUTGOING_WEB_PROXY_PASSWORD can be omitted. 

**Note:** An http proxy can also be configured manually in the central client configuration file at any time (see section "Custom Configuration Options").
    

## Initial configuration with Init Containers instead of init.conf file

Container orchestration platforms such as Kubernetes know the concept of so-called *Init Containers*. These utility containers are started before the actual container is started, with the task of performing initialization tasks.

If the initial configuration described above (with manually creating an init.conf file) is not a feasible way, one of the two provided init containers from sedex may be able to help.

There is a separate init container for each of the two initialization cases a) and b) described above. The task of the init containers is to create the init.conf file from passed ENV parameters.

### a) Init Container for an existing participant

If you already have a participant with an existing sedex participant certificate (a P12 file).

Run the sedex-client container using environment-specific values for the following options:
- --rm - Automatically remove the container when it exits
- /path/to/sedex-data - The path to the host's "sedex-data" directory or volume (holding the persisted data)
- SEDEX_ID - environment variable holding the participant's sedex id
- KEYSTORE - environment variable holding the *content* of the keystore file (P12) in base64 encoded format
- KEYSTORE_PW - environment variable holding the password to the keystore file
- OUTGOING_WEB_PROXY_HOST - optional environment variable holding the http proxy host
- OUTGOING_WEB_PROXY_PORT - optional environment variable holding the http proxy port
- OUTGOING_WEB_PROXY_USER - optional environment variable holding the http proxy user
- OUTGOING_WEB_PROXY_PASSWORD - optional environment variable holding the http proxy password
<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->


    $ docker run \
      --rm \
      --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
      --env SEDEX_ID=YOUR-SEDEX-ID \
      --env KEYSTORE=CONTENT-OF-YOUR-CERTIFICATE-FILE \
      --env KEYSTORE_PW=YOUR-PASSWORD \
      sedexch/sedex-client init-container-existing-cert.sh

**Note:** In a Linux console, the contents of the keystore file (P12) can be translated into the required base64 encoded format as follows:

    $ cat YOUR-CERTIFICATE-FILE.p12 | base64

Resulting output:

      MIIHdgIBAzCCBzwGCSqGSIb3DQEHAaCCBy0EggcpMIIHJTCCA+8GCSqGSIb3DQEHBqCCA+AwggPc
      AgEAMIID1QYJKoZIhvcNAQcBMBwGCiqGSIb3DQEMAQYwDgQIRYqcETINPCICAggAgIIDqJno8zFy
      [...]
      cDZCr7zStk1IBaD7WoHQqRmvlM9qJJrZsELaTflLcfo43GWGDiwX+OqTD0xo3J/EmVJ8fat/yKsM
      4lnSpDFaMCMGCSqGSIb3DQEJFTEWBBTPLjUs7MQ16yQIA6BqTg4C6uxj9jAzBgkqhkiG9w0BCRQx
      Jh4kAEMAbABpAGUAbgB0ACAAQwBlAHIAdABpAGYAaQBjAGEAdABlMDEwITAJBgUrDgMCGgUABBSF
      i3kuzz/2qGFiSHGVmHe+aHbm2wQIs0UZdpNO54oCAggA

**Note:** Since this content contains the private key of the sedex participant, it must be kept secret. This content and the corresponding password must not be visible to unauthorized persons at any time.


### b) initial configuration for a new participant

If you don't have an existing certificate but a *certificate request ID (CRID)* and a one time password (OTP).

Run the sedex-client container using environment-specific values for the following options:
- --rm - Automatically remove the container when it exits
- /path/to/sedex-data - The path to the host's "sedex-data" directory or volume (holding the persisted data)
- SEDEX_ID - environment variable holding the participant's sedex id
- CRID - environment variable holding the certificate request id (CRID)
- OTP - environment variable holding the one time password (OTP)
- OUTGOING_WEB_PROXY_HOST - optional environment variable holding the http proxy host
- OUTGOING_WEB_PROXY_PORT - optional environment variable holding the http proxy port
- OUTGOING_WEB_PROXY_USER - optional environment variable holding the http proxy user
- OUTGOING_WEB_PROXY_PASSWORD - optional environment variable holding the http proxy password
<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->


    $ docker run \
      --rm \
      --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
      --env SEDEX_ID=YOUR-SEDEX-ID \
      --env CRID=YOUR_CERTIFICATE_REQUEST_ID \
      --env OTP=YOUR-ONE-TIME-PASSWORD \
      sedexch/sedex-client init-container-new-cert.sh

## Running the sedex-client as a non-root user
Non-root container images add an extra layer of security and are generally recommended for production environments. The following steps 
describe how to set up a non-root group and user in case the host environment does not already provide these.

##### Step 1: Create a non-root group on the host machine
Determine a group ID (501 in the this example) that is unique on the host machine and create the group (the name does not have to be 
"sedex-group").

    $ groupadd --gid 501 sedex-group

##### Step 2: Create a non-root user on the host machine
Determine a user ID (901 in the this example) that is unique on the host machine and create the user (the name does not have to be 
"sedex-user"), assign it to the group created in the previous step, and specifying its home directory.

    $ useradd --uid 901 --no-log-init --gid sedex-group --create-home --home-dir /opt/sedexclient/ sedex-user

##### Step 3: Set the group and owner of the sedex-data directories
If the sedex-client will run as a non-root user inside of the Docker container, set the group and owner of the whole sedex-data directory 
tree and its contents so that the non-root user can access them.

    $ chown --recursive sedex-user:sedex-group /path/to/sedex-data

##### Step 4: Run the sedex-client container as a non-root user
Run the sedex-client container using environment-specific values for the following options:
- /path/to/sedex-data - The path to the host's "sedex-data" directory (holding the persisted data)
- YOUR_MONITORING_PORT - The port at which the monitoring web page of the sedex-client should be accessible from the outside of the container
- YOUR_WS_PROXY_HTTP_PORT - The unsecured http port at which the sedex-clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used.
- YOUR_WS_PROXY_HTTPS_PORT - The secured https port at which the sedex-clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used.
- --stop-timeout 65 - When the docker container is stopped (e.g. with "docker stop" command), the sedex-client's controller-stop.sh is executed to initiate a graceful 
  shutdown of the client. This typically takes longer than the default 10 seconds that the Docker daemon waits before killing the container.
  The --stop-timeout option overrides the default. The required time will vary from one installation to another.
- --restart unless-stopped - Restart Policy: Restart the container automatically after Docker daemon restarts, unless the container has been stopped intentionally. 
- --user - User: Sets the UID and GID used to start the sedex-client.
- -d - Detached: Start container in background without showing log output on console.
<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->


    $ docker run \
      --name sedex-client \
      --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
      --publish YOUR_MONITORING_PORT:8000 \
      --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
      --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
      --stop-timeout 65 \
      --restart unless-stopped \
      --user 901:501 \
      -d \
      sedexch/sedex-client



## Running a specific version of the container
If your are referencing the container by its name "sedexch/sedex-client" the version with the tag "latest" is automatically selected.
To use a specific sedex-client version and/or or a specific container version, use a versioned tag explicitly.
View the [list of available versions](https://hub.docker.com/r/sedexch/sedex-client/tags) in the  Docker hub registry.

**Note:** For simplicity the following examples the run parameters are ommited.
These parameters have to be set as described in the other run examples above.

Example: Run the latest Docker container of the latest sedex-client:

    $ docker run sedexch/sedex-client

Example: Run the latest Docker container of the sedex-client 5.3.1:

    $ docker run sedexch/sedex-client:5.3.1

Example: Run the Docker container 0.9-beta of the sedex-client 5.3.1:

    $ docker run sedexch/sedex-client:5.3.1_container-0.9-beta


## Pulling a specific image version
The "docker run" command does not automatically download an image from docker hub, if an older image with that name and tag is available locally.
In this case you have to manually issue a "docker pull" command, to download the newest images from docher hub.

Example: Get the latest Docker container of the latest sedex-client:

    $ docker pull sedexch/sedex-client

Example: Get the latest Docker container of the sedex-client 5.3.1:

    $ docker pull sedexch/sedex-client:5.3.1

Example: Get the Docker container 0.9-beta of the sedex-client 5.3.1:

    $ docker pull sedexch/sedex-client:5.3.1_container-0.9-beta



## Separate permanent stores for conf, log, etc.  
For the permanent store of data outside the container, most users will be happy to use one single directory "sedex-data" that holds all the different data types in one place:
- configuration
- sedex messages
- messaging state
- log files
- etc.

If you have good reasons to separate these stores, you can mount the different subdirectories seperately:

    $ mkdir /path/to/sedex-conf
    $ mkdir /path/to/sedex-interface
    $ mkdir /path/to/sedex-internal
    $ mkdir /path/to/sedex-logs
    $ mkdir /path/to/sedex-monitoring

    $ docker run \
      --name sedex-client \
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
      sedexch/sedex-client



