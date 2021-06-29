# Kubernetes & Co.

**The sedex Client Docker container can be run on a Kubernetes-based orchestration platform. The following points must be taken into account.**

## Memory Allocation
It is recommended to allocate at least 1GB memory for the sedex docker container. If additional containers will run within a Kubernetes Pod (e.g. side car proxies of a service mesh), their memory requirements must be added and more memory must be allocated to the Pod accordingly.


## Initial configuration with Init Containers instead of init.conf file

Container orchestration platforms such as Kubernetes know the concept of so-called *Init Containers*. These utility containers are started before the actual container is started, with the task of performing initialization tasks.

If the initial configuration described above (with manually creating an init.conf file) is not a feasible way, one of the two provided init containers from sedex may be able to help.

There is a separate init container for each of the two initialization cases a) and b) described in the [Initial Configuration with init-scripts](../initial-configuration/initial_configuration_with_init-scripts.md). The task of the init containers is to create the init.conf file from passed ENV parameters.


## Ensure a maximum of one client instance 

Due to the architecture of the sedex system, **a maximum of one sedex Client may be running for a participant at any time**. This must also be ensured when operating the sedex Client as a container.

On an orchestration platform for each container can be defined how many instances should run. E.g. Kubernetes calls this the number of *"replicas"*.

An orchestration platform can move containers to another node at any time. Usually this is done by starting a container on the new node before the old one is deactivated. This strategy may make sense for most web applications. However, when applied to the sedex Client, this would result in two sedex Clients running for a certain period of time. This can be prevented by setting the strategy to 
be used. Kubernetes calls the strategy to be chosen for sedex "Recreate".

The following is an excerpt from a Kubernetes deployment for the sedex Client:

        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: sedex Client-deployment
          labels:
            app: sedexch/sedex Client/1-123455-1
        spec:
          replicas: 1
          strategy:
            type: Recreate
          selector:
            matchLabels:
              app: sedexch/sedex Client/1-123455-1
          template:
            metadata:
              labels:
                app: sedexch/sedex Client/1-123455-1
            spec:
              containers:
              - name: sedexch/sedex Client
                image: sedexch/sedex Client:6.0.0
        [...]
