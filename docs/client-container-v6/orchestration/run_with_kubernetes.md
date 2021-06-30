# Kubernetes & Co.

**The sedex Client Docker container can be run on a Kubernetes-based orchestration platform. The following points must be taken into account.**

## Memory Allocation
It is recommended to allocate at least 1GB memory for the sedex docker container. If additional containers will run within a Kubernetes Pod (e.g. side car proxies of a service mesh), their memory requirements must be added and more memory must be allocated to the Pod accordingly.


## Initial configuration with Init Containers instead of init.conf file

Container orchestration platforms such as Kubernetes know the concept of so-called *Init Containers*. These utility containers are started before the actual container is started, with the task of performing initialization tasks.

If the initial configuration described above (with manually creating an init.conf file) is not a feasible way, one of the two provided init containers from sedex may be able to help.

There is a separate init container for each of the two initialization cases a) and b) described in the [Initial Configuration with init-scripts](../initial-configuration/initial_configuration_with_init-scripts.md). The task of the init containers is to create the init.conf file from passed ENV parameters.


## Ensure a maximum of one running client instance 

Due to the architecture of the sedex system, **a maximum of one sedex Client may be running for a participant at any time**. This must also be ensured when operating the sedex Client as a container.

On an orchestration platform for each container can be defined how many instances should run. E.g. Kubernetes calls this the number of *"replicas"*.

An orchestration platform can move containers to another node at any time. Usually this is done by starting a container on the new node before the old one is deactivated. This default strategy may make sense for most web applications. However, when applied to the sedex Client, this would result in two sedex Clients running for a short moment. This can be prevented by setting the replication strategy to be used. Kubernetes calls the strategy to be chosen for sedex "Recreate".

The following is an excerpt from a Kubernetes deployment shows this aspect of configuration for a sedex Client:

```yaml
    [...]
    spec:
      replicas: 1
      strategy:
        type: Recreate
    [...]
```

## Persistent Volume

It must be ensured that the volume for the external data storage is made persistent, i.e. has a lifetime independent of the container.

This can be achieved with Kubernetes by means of a so-called persistentVolumeClaim. 

The following is an excerpt from a Kubernetes deployment shows this aspect of configuration for a sedex Client:

```yaml
    [...]
        spec:
          volumes:
            - name: sedex-client-YOUR_SEDEX_ID-volume
              persistentVolumeClaim:
                claimName:  sedex-client-YOUR_SEDEX_ID-volume-claim
    [...]
```



## Example Kubernetes Deployment

Below you find a basic Kubernetes deployment file. In the first tab as *template*, in which you can replace the parameter YOUR_SEDEX_ID with your own sedex-ID, and in the second tab as *concrete example* for a fictitious sedex-ID 1-123456-1.

=== "Template"
    ``` yaml
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: sedex-client-YOUR_SEDEX_ID-deployment
      labels:
        app: sedex-client-YOUR_SEDEX_ID
    spec:
      replicas: 1
      strategy:
        type: Recreate
      selector:
        matchLabels:
          app: sedex-client-YOUR_SEDEX_ID
      template:
        metadata:
          labels:
            app: sedex-client-YOUR_SEDEX_ID
        spec:
          volumes:
            - name: sedex-client-YOUR_SEDEX_ID-volume
              persistentVolumeClaim:
                claimName:  sedex-client-YOUR_SEDEX_ID-persistent-volume-claim
          containers:
          - name: sedex-client
            image: sedexch/sedex-client:latest
            ports:
            - containerPort: 8000
            - containerPort: 8080
            - containerPort: 8443
            volumeMounts:
            - mountPath: "/sedex-data"
              name: sedex-client-YOUR_SEDEX_ID-volume
            livenessProbe:
              exec:
                command:
                - /opt/sedex-client/scripts/check-health.sh
              initialDelaySeconds: 5
              periodSeconds: 5
            readinessProbe:
              httpGet:
                path: /monitoring
                port: 8000
              initialDelaySeconds: 5
              periodSeconds: 5
    ```

=== "Example (sedex-ID 1-123456-1)"
    ``` yaml
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: sedex-client-1-123456-1-deployment
      labels:
        app: sedex-client-1-123456-1
    spec:
      replicas: 1
      strategy:
        type: Recreate
      selector:
        matchLabels:
          app: sedex-client-1-123456-1
      template:
        metadata:
          labels:
            app: sedex-client-1-123456-1
        spec:
          volumes:
            - name: sedex-client-1-123456-1-volume
              persistentVolumeClaim:
                claimName:  sedex-client-1-123456-1-persistent-volume-claim
          containers:
          - name: sedex-client
            image: sedexch/sedex-client:latest
            ports:
            - containerPort: 8000
            - containerPort: 8080
            - containerPort: 8443
            volumeMounts:
            - mountPath: "/sedex-data"
              name: sedex-client-1-123456-1-volume
            livenessProbe:
              exec:
                command:
                - /opt/sedex-client/scripts/check-health.sh
              initialDelaySeconds: 5
              periodSeconds: 5
            readinessProbe:
              httpGet:
                path: /monitoring
                port: 8000
              initialDelaySeconds: 5
              periodSeconds: 5
    ```