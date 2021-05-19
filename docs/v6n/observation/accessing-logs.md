# Accessing the Log Files of the sedex Client

**The logs of the sedex Client provide detailed information about running processes and important events. The log files should be monitored and analyzed - especially in case of errors.**

The following describes how to access the logs of the sedex Client.


## Option 1: Via Container Logs

The combined sedex Client logs are written to standard output `stdout` and thus can be accessed using the the standard container mechanisms for logs.
 
The following example shows how to continuously display the logs using `docker logs` command:

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


## Option 2: Via sedex-data's logs subdirectory

The sedex Client logs are written to specific files within subdirectories of the permanent data storage ("sedex-data"):

 - `logs/controller/controller-technical.log`: technical log for the main component of the sedex Client
 - `logs/adapter/adapter-technical.log`: technical log for the sedex messaging component (for sending/receiving sedex messages)
 - `logs/wsproxy/wsproxy-technical.log`: technical log for the sedex web service proxy component (for secure access to web services)
 - `logs/error-log.csv`: business log with important error events
 - `logs/receive-log.csv`: business log of received sedex messages
 - `logs/send-log.csv`: business log of sent sedex messages
 - `logs/wsproxy-access-log.csv`: business log of secure web service calls through sedex web service proxy


The following example lists the content of the `sedex-data/logs` folder:

```console
$ cd /path/to/sedex-data/logs
$ ls
adapter  controller  wsproxy  error-log.csv  receive-log.csv  send-log.csv  wsproxy-access-log.csv
```

!!! info
    The log files in `sedex-data/logs` are automatically rotated when they reach a certain size. Older logs are renamed and numbered consecutively.


