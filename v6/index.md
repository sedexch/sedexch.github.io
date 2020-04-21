**Documentation for the current sedex-client V6 Docker Image on [docker hub](https://hub.docker.com/r/sedexch/sedex-client)**

***


# Basic Documentation


This is the documentation for the official Docker image of the sedex-client software which can be found on [Docker Hub](https://hub.docker.com/r/sedexch/sedex-client). This documentation describes how to run a containerized version of the client for the **s**wiss s**e**cure **d**ata **ex**change (sedex) platform.

For more information about sedex and the sedex-client, visit [www.sedex.ch](http://www.sedex.ch).

![sedex logo](https://raw.githubusercontent.com/sedexch/web-resources/master/sedex-logo.png)


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
 Please tell us what you like and what you don't like at sedexsupport@bfs.admin.ch.


## Supported Tags

| Tag(s) | Image Description | Release date |
|---|---|---|
| **latest**, 6.0.0, 6.0.0_container-0.96-beta | The most current container image (version 0.95-beta) with sedex-client 6.0.0. **Recommended.** | April 21, 2020 |
| 5.3.1, 5.3.1_container-0.94-beta | The legacy container image (version 0.94-beta) with sedex-client 5.3.1. **Legacy.** | December 19, 2019 |

**Note:**
The special tag "**latest**" will always be an alias for the most current *container* of the most current *sedex-client*.


---
# What is sedex-client?

[**sedex**](http://www.sedex.ch) stands for *secure data exchange* and is a service of the [Swiss Federal Statistical Office (FSO)](https://www.bfs.admin.ch/bfs/en/home.html).

At its core, the platform is a *message-oriented middleware* designed for secure asynchronous data exchange between business applications of different organizational units. In specific cases, synchronous data exchange (SOAP and RESTful web services) also takes place. The sedex platform is highly available (24/7).

The **sedex-client** is a software which is installed at the business applications and offers the interface between the business applications and the remote sedex server and message hub.



---
# Introduction

In order to successfully operate the sedex-client as a Docker container, you should first understand the following concepts.

## Permanent Data Storage ("sedex-data")
The sedex-client must be able to store certain data permanently outside of the container. All the following sedex specific data must survive the lifespan of a Docker container:
- configuration
- sedex messages
- messaging state
- log files
- etc.

For the permanent storage of data outside the container, Docker primarily offers the following two options ([see Docker documentation](https://docs.docker.com/storage/) for details):
- *Volumes* (storage managed by Docker)
- *Bind Mounts* (local file system based storage on the host)

**Notes:**
 - The following command line examples only show the use of *bind mounts*. Experienced Docker administrators can easily adapt the examples to *volumes*.
 - In our examples the directory with the permanent data storage is named "sedex-data". Of course you can name this directory differently, but then you have to adapt the examples to your actual directory name. 


## Initial configuration parameters
The very first time the Docker sedex client is started, the permanent storage directory "sedex-data" is initialized.
I.e. a certain directory structure is created and some necessary configuration files are created and stored in this structure.

In order to start this initialization process at its first start, the sedex-clients needs a few initial configuration parameters (e.g. the participant's sedexId) provided in the form of a plain text file named "init.conf".

The content of this file "init.conf" depends on which of the following two cases a sedex-client is to be used:

 **a) For an existing sedex participant:**
This means that a sedex-client has been running for this participant before. Thus a sedex *participant-certificate* and a corresponding *password* already exist and must be reused.

 **b) For a new sedex participant:**
This means that there has no sedex-client been running for this participant before. Thus there is *no* participant-certificate yet but a so-called *certificate request id (CRID)* and a corresponding *one time password (OTP)*.

The installation instructions will distinguish between these two cases a) and b).



---
# Requirements
Docker Engine should be v17.06 or later. Older versions are not supported and the api syntax may differ from this manual.

# How to use this image

## I) Initial Configuration

The following manual preparation steps only have to be executed **just once before the very first start of the sedex-client**.


### Step 1: Create a sedex-data directory
Create a directory "sedex-data" on the local host or a network attached storage (NAS) where the sedex-client will store files that have to be persisted 
outside of the Docker container (ex. the sedex messaging interface directories). This directory will be used as a Docker bind mount.

    $ mkdir /path/to/sedex-data


### Step 2: Create a subdirectory conf for the configuration
Create a subdirectory inside of "sedex-data" directory. This directory will contain all configuration files for this sedex participant.

    $ mkdir /path/to/sedex-data/conf


### Step 3a: Provide the initial configuration for an *existing* participant

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
    
**Note:** After the first successful start of the sedex-client, this file will be **deleted automatically** as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section [Advanced Docker Options](advanced-docker-options.html)) or later manually be defined in the central configuration file of the sedex client (see section [Advanced sedex Client Configuration](sedex-client-configuration-options.html)). 

    
### Step 3b: Provide the initial configuration for a *new* participant

Note: Execute this step only for *new* sedex participant with no existing certificate but instead with a *certificate request id (CRID)* and a *one time password (OTP)*. 
For new participants follow Step 3a.

##### Provide the file with the initial configuration parameters

With a text editor (e.g. "nano") create a plain text file named "init.conf" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/init.conf

Add the following three configuration parameters (with values adjusted to your actual data) to your "init.conf" file and save it:

    SEDEX_ID=YOUR-SEDEX-ID
    SEDEX_CRID=YOUR_CERTIFICATE_REQUEST_ID
    SEDEX_OTP=YOUR-ONE-TIME-PASSWORD

**Note:** After the first successful start of the sedex-client, this file will be **deleted automatically** as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section [Advanced Docker Options](advanced-docker-options.html)) or later manually be defined in the central configuration file of the sedex client (see section [Advanced sedex Client Configuration](sedex-client-configuration-options.html)). 


## II) Starting and using the sedex-client

The following steps show how to create and start the latest Docker container running the sedex-client.

**Note:** If you want to use a specific version of the container then see [Advanced Docker Options](advanced-docker-options.html) for details.

### Run the sedex-client container
Run the sedex-client container using environment-specific values for the following options:
- /path/to/sedex-data - The path to the host's "sedex-data" directory (holding the persisted data)
- YOUR_MONITORING_PORT - The port at which the monitoring web page of the sedex-client should be accessible from the outside of the container
- YOUR_WS_PROXY_HTTP_PORT - The unsecured http port at which the sedex-clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used or will only be used via https.
- YOUR_WS_PROXY_HTTPS_PORT - The secured https port at which the sedex-clients Web service proxy should be accessible from the outside of the
  container. **Note:** You can omit this line if the WS-Proxy will not be used.
- --stop-timeout 65 - When the docker container is stopped (e.g. with "docker stop" command), the sedex-client's controller-stop.sh is executed to initiate a graceful 
  shutdown of the client. This typically takes longer than the default 10 seconds that the Docker daemon waits before killing the container.
  The --stop-timeout option overrides the default. The required time will vary from one installation to another.
- --restart unless-stopped - Restart Policy: Restart the container automatically after Docker daemon restarts, unless the container has been stopped intentionally. 
- -d - Detached: Start container in background without showing log output on console.
<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->

To run the sedex-client container enter the following commands:

    $ docker pull sedexch/sedex-client:latest
    
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


**Note:** After the very first start of the sedex-client Docker container, a general default configuration is created.
If specific configuration options are required, these can be set in the central configuration file of the sedex-client.
With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf" and edit the contained configuration parameters as needed and as described in [Advanced sedex Client Configuration](sedex-client-configuration-options.html):

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
The standard sedex-client log files are located in sedex-data's logs subdirectory.

    $ cd /path/to/sedex-data/logs
    $ ls
    adapter  controller  wsproxy  error-log.csv  receive-log.csv  send-log.csv  wsproxy-access-log.csv


### Monitoring the sedex-client container

It is essential to integrate sedex-client's functionality in your own operations monitoring system. The key/value pairs provided by the sedex client for this purpose can be accessed via file system or via http. The basic status of the container can also be checked via docker health.

##### Option 1: Via sedex-data's monitoring subdirectory (file system)

The sedex-client monitoring file is a plain text file and is located in sedex-data's monitoring subdirectory.

    $ cd /path/to/sedex-data/monitoring

##### Option 2: Via the monitoring Web page (http)

The sedex-client monitoring webpage is a plain text page and is accessible via the mapped port (default 8000).

    $ curl http://IP-DOCKER-HOST:8000/monitoring

##### Option 3: Via docker health

Use the standard docker diagnostics to check the global status and health of a container.

**Note:** Nevertheless it is recommended to check the most important key/value pairs on the monitoring page of the sedex client.


