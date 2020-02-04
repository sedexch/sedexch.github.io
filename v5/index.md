**Documentation for the legacy Docker Image sedex-client V5 availabe on [docker hub](https://hub.docker.com/r/sedexch/sedex-client)**

[Switch to the documentation for the current sedex-client V6](/v6)


***


This is the documentation for the official Docker image of the sedex-client software. The docker image can be found on ducker hub and allows to run a containerized version of the client for the Swiss SEcure Data EXchange (sedex) platform.
For more information about sedex and the sedex-client, visit [www.sedex.ch](http://www.sedex.ch).

![sedex logo](https://raw.githubusercontent.com/sedexdev/sedexdev/master/sedex-logo.png)

**Important Notes:**
 - **Private application:**
sedex is a closed user group for registered and authorized participants only.
If you are not such a registered participant, you cannot use sedex-client.

 - **Warning:**
This image is currently in **EXPERIMENTAL** status and for selected early adopters only ("beta version only").
It is strongly discouraged to run a productive sedex-client based on this image without the necessary knowledge.
Please contact [sedex support](http://www.sedex.ch) should you plan to use this image or have any questions.

- **One instance only:**
It is absolutely necessary to prevent two or more sedex clients from running simultaneously for one participant.
In the worst case sedex messages could be lost.
Therefore it has to be ensured that only one sedex client is running at a time.
This has to be considered especially when operating on automatic container orchestration platforms like Kubernetes.

- **Feedback:**
 We are very interested in feedback on the Docker version of the sedex-client and this documentation.
 Please tell us what you like and what you don't like.


## Current Docker Hub Tags

| Tag(s) | Image Description | Release date |
|---|---|---|
| latest, 5.3.1, 5.3.1_container-0.94-beta | The most current container image (version 0.94-beta) with sedex-client 5.3.1. | December 19, 2019 |

**Note:**
The special tag "latest" will always be an alias for the most current container of the most current sedex-client.


---
# What are sedex and sedex-client

[**sedex**](http://www.sedex.ch) stands for *secure data exchange* and is a service of the [Swiss Federal Statistical Office (FSO)](https://www.bfs.admin.ch/bfs/en/home.html).

At its core, the platform is a *message-oriented middleware* designed for secure asynchronous data exchange between business applications of different organizational units. In specific cases, synchronous data exchange (web services) also takes place. The sedex platform is highly available (24/7).

The **sedex-client** is a software which is installed at the business applications and offers the interface between the business applications and the remote sedex server and message hub.



---
# Introduction

In order to successfully operate the sedex-client as a Docker container, you should first understand the following concepts.

## Permanent Data Storage ("sedex-data")
The sedex-client must be able to store certain data permanently. All this data must survive a restart of the Docker container:
- configuration
- sedex messages
- messaging state
- log files
- etc.

For the permanent storage of data outside the container, Docker primarily offers the following two options ([see Docker documentation](https://docs.docker.com/storage/) for details):
- *Volumes* (storage managed by Docker)
- *Bind Mounts* (storage on the host system)

**Notes:**
 - The following command line examples only show the use of *bind mounts*. Experienced Docker administrators can easily adapt the examples to *volumes*.
 - In our examples the directory with the permanent data storage is named "sedex-data". Of course you can name this directory differently, but then you have to adapt the examples to your actual directory name. 


## Initial configuration parameters
The very first time the Docker sedex client is started, the permanent storage directory "sedex-data" is initialized.
I.e. a certain directory structure is created and some needed configuration files are created and stored in this structure.

In order to start this initialization process at its first start, the sedex-clients needs a few initial configuration parameters (e.g. the participant's sedexId) provided in the form of a plain text file named "init.conf".

The content of this file "init.conf" depends on which of the following two cases a sedex-client is to be used:

 **a) For an existing sedex participant:**
This means that the sedex-client has been running for this participant before. Thus a sedex participant-certificate and a corresponding password already exist and must be reused.

 **b) For a new sedex participant:**
This means that there has no sedex-client been running for this participant before. Thus there is no participant-certificate yet but a so-called *certificate request id (CRID)* and a corresponding one time password (OTP).

The following instructions will distinguish between these two cases.



---
# Requirements
Docker Engine should be v17.06 or later. Older versions are not supported and the api syntax may differ from this manual.

# How to use this image

## I) Initial Configuration

The following manual preparation steps only have to be executed once before the very first start of the sedex-client.


### Step 1: Create a sedex-data directory
Create a directory "sedex-data" on the local host or a network attached storage (NAS) where the sedex-client will store files that have to be persisted 
outside of the Docker container (ex. the sedex messaging interface directories). This directory will be used as a Docker bind mount.

    $ mkdir /path/to/sedex-data


### Step 2: Create a subdirectory conf for the configuration
Create a subdirectory inside of "sedex-data" directory. This directory will contain all configuration files for this sedex participant.

    $ mkdir /path/to/sedex-data/conf


### Step 3a: Provide the initial configuration for an existing participant

Note: Execute this step only, if a sedex participant-certificate and a corresponding password already exist.
For new participants skip to Step 3b.

##### Provide the existing sedex certificate file (P12)
Copy the sedex keystore file (PKCS12/P12 format, containing the private key and certificate) into the "sedex-data/conf" subdirectory.

    $ cp /path/to/credentials/YOUR-CERTIFICATE-FILE.p12 /path/to/sedex-data/conf/

##### Provide the file with the initial configuration parameters

With a text editor (e.g. "nano") create a plain text file named "init.conf" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/init.conf

Add the following three configuration parameters (with values adjusted to your actual data) to your "init.conf" file and save it:

    SEDEX_ID=YOUR-SEDEX-ID
    SEDEX_CERTIFICATE_FILENAME=YOUR-CERTIFICATE-FILE.p12
    SEDEX_CERTIFICATE_PASSWORD=YOUR-PASSWORD
    
**Note:** After the first successful start of the sedex-client, this file will be deleted automatically as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section "Advanced Usage Options") or later manually be defined in the central configuration file of the sedex client (see section "Custom Configuration Options"). 

    
### Step 3b: Provide the initial configuration for a new participant

Note: Execute this step only for new sedex participant with no existing certificate but instead with a certificate request id (CRID) and a one time password (OTP). 
For new participants follow Step 3a.

##### Provide the file with the initial configuration parameters

With a text editor (e.g. "nano") create a plain text file named "init.conf" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/init.conf

Add the following three configuration parameters (with values adjusted to your actual data) to your "init.conf" file and save it:

    SEDEX_ID=YOUR-SEDEX-ID
    SEDEX_CRID=YOUR_CERTIFICATE_REQUEST_ID
    SEDEX_OTP=YOUR-ONE-TIME-PASSWORD

**Note:** After the first successful start of the sedex-client, this file will be deleted automatically as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section "Advanced Usage Options") or later manually be defined in the central configuration file of the sedex client (see section "Custom Configuration Options"). 


## II) Starting and using the sedex-client

The following steps show how to create and start the latest Docker container running the sedex-client.

### Run the sedex-client container
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
      -d \
      sedexch/sedex-client


**Note:** After the very first start of the sedex-client Docker container, a general default configuration is created.
If specific configuration options are required, they can be set in the central configuration file of the sedex-client.
With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf" and edit the contained configuration parameters as needed:

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

If no such configuration file has been created after the very first start of the sedex-client, you can search for the cause in the Docker log file:

    $ docker logs sedex-client
    

### Stop and start the sedex-client container

Stop the sedex-client container:

    $ docker stop sedex-client

Start the sedex-client container:

    $ docker start sedex-client

### Accessing log files of the sedex-client container

##### Option 1: Via docker logs
The combined sedex-client logs can be accessed via the standard docker mechanism.

    $ docker logs sedex-client --follow
    14:21:27 INFO Adapterlog - 14:21:27 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
    14:21:57 INFO Adapterlog - 14:21:57 DEBUG ThrottlingPriorityMessageScheduler - Begin scheduling of messages
    14:21:57 INFO Adapterlog - 14:21:57 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
    14:22:27 INFO Adapterlog - 14:22:27 DEBUG ThrottlingPriorityMessageScheduler - Begin scheduling of messages
    14:22:27 INFO Adapterlog - 14:22:27 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
    14:22:57 INFO Adapterlog - 14:22:57 DEBUG ThrottlingPriorityMessageScheduler - Begin scheduling of messages
    14:22:57 INFO Adapterlog - 14:22:57 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
    [...]

##### Option 2: Via sedex-data's logs subdirectory
The normal sedex-client log files are in sedex-data's logs subdirectory.

    $ cd /path/to/sedex-data/logs
    $ ls
    adapter  container-technical.log  controller  error.log  receive.log  send.log  wsproxy


### Monitoring the sedex-client container

##### Option 1: Via sedex-data's monitoring subdirectory

The normal sedex-client monitoring file is in sedex-data's monitoring subdirectory.

    $ cd /path/to/sedex-data/monitoring

##### Option 2: Via the monitoring Web page
You find the usual sedex-client monitoring webpage accessible via the mapped port:

    $ curl http://IP-DOCKER-HOST:8000/monitoring

##### Option 3: Via docker health
**Note:** This option has not yet been documented.



---
# Advanced Usage Options

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

## Custom Configuration options

**Note:**
Configuration changes become active after a *restart* of the container.
So restart the container to activate any changes made to the central configuration file "sedex-client-configuration.properties".


### Internet access via HTTP-Proxy
If an HTTP proxy is required to access the Internet, such a proxy server can be entered in the central sedex-client configuration file.

Example:

    File: /path/to/sedex-data/conf/sedex-client-configuration.properties
    ...
    # Proxy settings: Set these values only if you reach the Internet in your network only through a proxy
    # Example: host=proxy.example.ch port=8080 user=example-user password=1234
    outgoing-web-proxy.host=proxy.example.ch
    outgoing-web-proxy.port=8080
    outgoing-web-proxy.user=example-user
    outgoing-web-proxy.password=1234
    ...

**Note:** Leave the user/password parameters blank if proxy server authentication is not required. Restart container to activate Changes.


### Timezone
The default timezone in the sedex-client Docker container is "Europe/Zurich" (i.e. local Swiss time).
If you have to set another timezone (e.g. UTC) then set the environment variable TZ by adding the following option to your container run statement:

    --env TZ=UTC   

### Disable WebSocket protocol
By default, the sedex-client uses the WebSocket protocol to reduce round trip times by actively notifying the client via events from the server.

In extremely rare cases, the WebSocket protocol can cause communication problems with network components.
If such a problem is detected, the use of WebSocket can be deactivated.
As a result, the client can only use so-called polling, which leads to longer round trip times, but solves the problems.

With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Add the following configuration parameter to the configuration file and save it:

    websocket.enabled=false
    
**Note:** Restart container to activate changes    

### Disable sedex Webservice Proxy
By default, the sedex-client is starting the sedex Webservice Proxy functionality. If not used, the sedex Webservice Proxy can be disabled in order to save some resources (CPU, memory).


With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Set the following configuration parameter in the configuration file to false and save it:

    wsproxy.start=false

**Note:** Restart container to activate changes

### Custom sedex Webservice Proxy endpoints

When developing or testing a Web service, it may be necessary to configure test instances of services.
The endpoint of a service provider behind the local Web service proxy endpoint can be configured in the following 
configuration file:

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Adding the following line overrides the endpoint for the Web service *my-service*:

    wsproxy.services.my-service.target-url=https://www.example.com/path  

e.g. for UPIQueryService:
    
    wsproxy.services.UPIQueryService.target-url=https://wupi-test.zas.admin.ch/wupi_cc/UPIQueryService 

If no entry is made for a particular service, the default endpoint is used.


