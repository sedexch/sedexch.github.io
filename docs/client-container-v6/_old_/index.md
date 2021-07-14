# Basic Usage of sedex Client Docker Container V6

!!! warning
    This image is currently in **EXPERIMENTAL** status and for selected early adopters only ("beta version only").
    
    It is strongly discouraged to run a productive sedex Client based on this image without the necessary knowledge.
    Please contact [sedex support](http://www.sedex.ch) should you plan to use this image or have any questions.


## Important Notes

 - **Private application:**
sedex is a closed user group for registered and authorized participants only.
If you are not such a registered participant, you cannot use sedex Client.

- **One running instance only:**
It is absolutely necessary that you prevent two or more sedex Clients from running simultaneously for the same participant.
In the worst case sedex messages could be lost.
Therefore it has to be ensured that only one sedex Client is running at a time.
This has to be considered especially when operating on automatic container orchestration platforms like Kubernetes. See section [Kubernetes & Co.](orchestration.md) for further details.

- **Persistent file storage for data volume required:**
There is important data that has to be stored permenently outside of the sedex Client Docker container. Examples for such data are messages, receipts, local messaging state database, certificates, passwords etc. If this data is not reliably stored outside the container, there is a risk of message loss and loss of access to the own message inbox. See section [*Permanent Data Storage ("sedex-data")*](#Permanent_Data_Storage) for further details.

- **Feedback:**
We are very interested in feedback on the Docker version of the sedex Client and this documentation.
 Please tell us what you like and what you don't like at sedexsupport@bfs.admin.ch.


## Current Docker Hub Tags

| Tag(s) | Image Description | Release date |
|---|---|---|
| **latest**, 6.0.4, 6.0.4_container-0.99-beta | The most current container image (version 0.99-beta) with sedex Client 6.0.4. **Recommended.** | April 07, 2021 |
| 5.3.1, 5.3.1_container-0.94-beta | The legacy container image (version 0.94-beta) with sedex Client 5.3.1. **Legacy.** | December 19, 2019 |

**Note:**
The special tag "**latest**" will always be an alias for the most current *container* of the most current *sedex Client*. The tags available can 


## Requirements
 * **Docker Version:** Docker Engine should be v17.06 or later. Older versions are not supported and the api syntax may differ from this manual.
 * **Memory:** It is recommended to allocate at least 1GB memory for the sedex docker container.


## Introduction

In order to successfully operate the sedex Client as a Docker container, you should first understand the following concepts.

<a name="Permanent_Data_Storage"></a>
### Permanent Data Storage ("sedex-data")
The sedex Client must be able to store certain data permanently outside of the container. All the following sedex specific data must survive the lifespan of a Docker container:

- configuration
- sedex messages
- messaging state
- log files
- etc.

For the permanent storage of data outside the container, Docker primarily offers the following two options ([see Docker documentation](https://docs.docker.com/storage/) for details):

- *Volumes* (storage managed by Docker)
- *Bind Mounts* (local file system based storage on the host)

**Notes:**

 - The following command line examples only show the use of *bind mounts*. Experienced container administrators can easily adapt the examples to *volumes*.
 - In our examples the directory with the permanent data storage is named "sedex-data". Of course you can name this directory differently, but then you have to adapt the examples to your actual directory name. 


### Initial configuration parameters

If you are starting from scratch, i.e. you have never started a sedex Client as a Docker container for this one participant, then the sedex container must initialize the permanent storage directory "sedex-data" once before the very first start so that it can work with it later.
Specifically, the container will automatically create the required directory structure and create necessary configuration files in it..

!!! note

    In order to start this initialization process at its first start, the sedex Clients needs a few initial configuration parameters (e.g. the participant's sedexId) provided in the form of a plain text file named "init.conf".

The **content of the initial configuration file "init.conf"** depends on the form of the credentials for the participant you have. You will find yourself in one of the following two initial cases:

   - **Case a) Initialize for an existing sedex participant:**
  This means that a sedex Client has been running in another installation for this participant before. Thus a sedex *participant-certificate* and a corresponding *password* already exist and must be reused.

   - **Case b) Initialize for a new sedex participant:**
  This means that there has no sedex Client been running for this participant before. Thus there is *no* participant-certificate yet but a so-called *certificate request id (CRID)* and a corresponding *one time password (OTP)*.

The following installation instructions will distinguish between these two init cases a) and b).





## How to use this image

### I) Initial Configuration

!!! warning

    The initial preparation steps only have to be executed **just once before the very first start of the sedex Client**.


#### Step 1: Create a sedex-data directory
Create a directory "sedex-data" on the local host or a network attached storage (NAS) where the sedex Client will store files that have to be persisted 
outside of the Docker container (ex. the sedex messaging interface directories). This directory will be used as a Docker bind mount.
```console
$ mkdir /path/to/sedex-data
```


#### Step 2: Create a subdirectory conf for the configuration
Create a subdirectory inside of "sedex-data" directory. This directory will contain all configuration files for this sedex participant.
```console
$ mkdir /path/to/sedex-data/conf
```


#### Step 3a: Provide the initial configuration for an *existing* participant

!!! note

    Execute this step only, if a sedex participant-certificate and a corresponding password already exist.
    For new participants skip to Step 3b.

##### Provide the existing sedex certificate file (P12)
Copy the sedex keystore file (PKCS12/P12 format, containing the private key and certificate) into the "sedex-data/conf" subdirectory.
```console
$ cp /path/to/credentials/YOUR-CERTIFICATE-FILE.p12 /path/to/sedex-data/conf/
```

##### Provide the file with the initial configuration parameters

With a text editor (e.g. "nano") create a plain text file named "init.conf" in the subdirectory "sedex-data/conf":
```console
$ nano /path/to/sedex-data/conf/init.conf
```

Add the following three configuration parameters (with values adjusted to your actual data) to your "init.conf" file and save it:

```console
SEDEX_ID=YOUR-SEDEX-ID
SEDEX_CERTIFICATE_FILENAME=YOUR-CERTIFICATE-FILE.p12
SEDEX_CERTIFICATE_PASSWORD=YOUR-PASSWORD
```
    
**Note:** After the first successful start of the sedex Client, the file init.conf will be **deleted automatically** as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section [Advanced Docker Options](advanced-docker-options)) or later manually be defined in the central configuration file of the sedex Client (see section [Advanced sedex Client Configuration](sedex Client-configuration-options)). 

    
#### Step 3b: Provide the initial configuration for a *new* participant

!!! note

    Execute this step only for *new* sedex participant with no existing certificate but instead with a *certificate request id (CRID)* and a *one time password (OTP)*. 
    For new participants follow Step 3a.

##### Provide the file with the initial configuration parameters

With a text editor (e.g. "nano") create a plain text file named "init.conf" in the subdirectory "sedex-data/conf":

```console
$ nano /path/to/sedex-data/conf/init.conf
```

Add the following three configuration parameters (with values adjusted to your actual data) to your "init.conf" file and save it:

```console
SEDEX_ID=YOUR-SEDEX-ID
SEDEX_CRID=YOUR_CERTIFICATE_REQUEST_ID
SEDEX_OTP=YOUR-ONE-TIME-PASSWORD
```

**Note:** After the first successful start of the sedex Client, the file init.conf will be **deleted automatically** as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section [Advanced Docker Options](advanced-docker-options)) or later manually be defined in the central configuration file of the sedex Client (see section [Advanced sedex Client Configuration](sedex Client-configuration-options)). 


### II) Starting and using the sedex Client

The following steps show how to create and start the latest Docker container running the sedex Client.

**Note:** If you want to use a specific version of the container then see [Advanced Docker Options](advanced-docker-options) for details.

#### Run the sedex Client container
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
$ docker pull sedexch/sedex-client:latest

$ docker run \
  --name sedex Client \
  --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
  --publish YOUR_MONITORING_PORT:8000 \
  --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
  --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
  --stop-timeout 65 \
  --restart unless-stopped \
  -d \
  sedexch/sedex-client:latest
```


**Note:** After the very first start of the sedex Client Docker container, a general default configuration is created.
If specific configuration options are required, these can be set in the central configuration file of the sedex Client.
With a text editor (e.g. "nano") open the central sedex Client configuration file named "sedex Client-configuration.properties" in the subdirectory "sedex-data/conf" and edit the contained configuration parameters as needed and as described in [Advanced sedex Client Configuration](sedex Client-configuration-options):
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

#### Accessing log files of the sedex Client container

##### Option 1: Via docker logs
The combined sedex Client logs can be accessed via the standard docker mechanism.

```console
$ docker logs sedex Client --follow
14:21:27 INFO Adapterlog - 14:21:27 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
14:21:57 INFO Adapterlog - 14:21:57 DEBUG ThrottlingPriorityMessageScheduler - Begin scheduling of messages
14:21:57 INFO Adapterlog - 14:21:57 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
14:22:27 INFO Adapterlog - 14:22:27 DEBUG ThrottlingPriorityMessageScheduler - Begin scheduling of messages
14:22:27 INFO Adapterlog - 14:22:27 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
14:22:57 INFO Adapterlog - 14:22:57 DEBUG ThrottlingPriorityMessageScheduler - Begin scheduling of messages
14:22:57 INFO Adapterlog - 14:22:57 DEBUG ThrottlingPriorityMessageScheduler - No new messages in outbox. Skipping scheduling of messages to be sent.
[...]
```

##### Option 2: Via sedex-data's logs subdirectory
The standard sedex Client log files are located in sedex-data's logs subdirectory.

```console
$ cd /path/to/sedex-data/logs
$ ls
adapter  controller  wsproxy  error-log.csv  receive-log.csv  send-log.csv  wsproxy-access-log.csv
```


#### Monitoring the sedex Client container

It is essential to integrate sedex Client's functionality in your own operations monitoring system. The key/value pairs provided by the sedex Client for this purpose can be accessed via file system or via http. The basic status of the container can also be checked via docker health.

##### Option 1: Via sedex-data's monitoring subdirectory (file system)

The sedex Client monitoring file is a plain text file and is located in sedex-data's monitoring subdirectory.

```console
$ cd /path/to/sedex-data/monitoring
```

##### Option 2: Via the monitoring Web page (http)

The sedex Client monitoring webpage is a plain text page and is accessible via the mapped port (default 8000).

```console
    $ curl http://IP-DOCKER-HOST:8000/monitoring
```

##### Option 3: Via docker health

Use the standard docker diagnostics to check the global status and health of a container.

**Note:** Nevertheless it is recommended to check the most important key/value pairs on the monitoring page of the sedex Client.


