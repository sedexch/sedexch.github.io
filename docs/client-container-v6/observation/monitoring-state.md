# Monitoring the State of the sedex Client

**It is essential to check sedex Client's state in your own operations monitoring system.**

The following describes how the state of the sedex Client can be observed.


## Via the Monitoring Page of the sedex Client

The key/value pairs provided by the sedex Client for the purpose of operations monitoring can be accessed via http or via filesystem.


### Option 1: Via the monitoring Web page (http)

The sedex Client monitoring webpage is a plain text page and is accessible via the mapped port (default 8000).

```console
    $ curl http://DOCKER-HOST:8000/monitoring
```

### Option 2: Via sedex-data's monitoring subdirectory (file system)

The sedex Client monitoring file is a plain text file and is located in sedex-data's `monitoring` subdirectory.

```console
$ cd /path/to/sedex-data/monitoring
```


## Via Container Health State

The basic status of the container can also be checked via standard container health mechanisms.


```console
$ docker inspect sedex Client

       [...]
       "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 4804,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-05-10T14:31:30.906584007Z",
            "FinishedAt": "2021-05-06T19:53:02.091368316Z",
            "Health": {
                "Status": "healthy",
                "FailingStreak": 0,
            [...]
```


**Note:** The health state of the container is only very coarse. That's why it is recommended to include the most important key/value pairs of the of the sedex Client's monitoring page in your own operations monitoring system.


