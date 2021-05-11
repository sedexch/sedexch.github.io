# Introduction: sedex-Client Docker Container (V6)

!!! warning
    This image is currently in **EXPERIMENTAL** status and for selected early adopters only ("beta version only").
    
    It is strongly discouraged to run a productive sedex-client based on this image without the necessary knowledge.
    Please contact [sedex support](http://www.sedex.ch) should you plan to use this image or have any questions.


## Important Notes

 - **Private application:**
sedex is a closed user group for registered and authorized participants only.
If you are not such a registered participant, you cannot use sedex-client.

- **One running instance only:**
It is absolutely necessary that you prevent two or more sedex clients from running simultaneously for the same participant.
In the worst case sedex messages could be lost.
Therefore it has to be ensured that only one sedex client is running at a time.
This has to be considered especially when operating on automatic container orchestration platforms like Kubernetes.
See section [Run - with Kubernetes](kubernetes.md) for further details.

- **Permanent file storage for data volume required:**
There is important data that has to be stored permenently outside of the sedex-client Docker container.
Examples for such data are messages, receipts, local messaging state database, certificates, passwords etc.
If this data is not reliably stored/persisted outside the container, there is a risk of message loss and loss of access to the own message inbox.
See section [*Permanent Data Storage ("sedex-data")*](#Permanent_Data_Storage) for further details.

- **Feedback:**
We are very interested in your feedback on the Docker version of the sedex-client and this documentation.
Please tell us what you like and what you don't like at sedexsupport@bfs.admin.ch.




#### Accessing log files of the sedex-client container

##### Option 1: Via docker logs
The combined sedex-client logs can be accessed via the standard docker mechanism.

```console
$ docker logs sedex-client --follow
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
The standard sedex-client log files are located in sedex-data's logs subdirectory.

```console
$ cd /path/to/sedex-data/logs
$ ls
adapter  controller  wsproxy  error-log.csv  receive-log.csv  send-log.csv  wsproxy-access-log.csv
```


#### Monitoring the sedex-client container

It is essential to integrate sedex-client's functionality in your own operations monitoring system. The key/value pairs provided by the sedex client for this purpose can be accessed via file system or via http. The basic status of the container can also be checked via docker health.

##### Option 1: Via sedex-data's monitoring subdirectory (file system)

The sedex-client monitoring file is a plain text file and is located in sedex-data's monitoring subdirectory.

```console
$ cd /path/to/sedex-data/monitoring
```

##### Option 2: Via the monitoring Web page (http)

The sedex-client monitoring webpage is a plain text page and is accessible via the mapped port (default 8000).

```console
    $ curl http://IP-DOCKER-HOST:8000/monitoring
```

##### Option 3: Via docker health

Use the standard docker diagnostics to check the global status and health of a container.

**Note:** Nevertheless it is recommended to check the most important key/value pairs on the monitoring page of the sedex client.


