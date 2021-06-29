# Overview and Basic Concepts

**On this page you get an overview of the sedex client as a Docker container and the basic concepts involved.
Before you start using the sedex Client as a Docker container, it is important to understand this page.**



## Overview of the sedex Client as a Docker Container

With the official Docker image provided by the sedex service, you can run the current sedex Client as a Docker container. All functionality provided by sedex can be used comfortably with all the advantages of containers.

The diagram below shows the main components of the sedex Client Docker container.
The components are described individually below the diagram.

![Overview of the sedex Client as a Docker Container](/assets/v6/sedex-client-container-volume-overview-1.png)


<a name="Permanent_Data_Storage"></a>
### Permanent Data Storage ("sedex-data")

!!! info "Containers are disposable."

    Containers have, by principle, a disposable character.
    This means that any data stored in the container is also deleted with the container.
    However, sedex needs data that survive the (possibly short) life cycle of a container and must be persisted permanently.
    For this purpose, an external permanent data storage must be connected to the sedex Client container.

In the *external permanent data storage* (called "sedex-data"), the sedex Client container persists the following data in subfolders:

- **conf/**<br /> Configuration data of the sedex Client. Including participant certificate and private key. Keep them safe and secure!
- **interface/**<br /> File system interface for the business applications, contains among others the message inbox and message outbox.
- **internal/**<br /> Technical data required by the sedex client. Only useful for the sedex client itself.
- **logs/**<br /> Logfiles of the sedex client with events and errors mainly for your operation personnel.
- **monitoring/**<br /> A simple text page with state information and metrics for your operations monitoring.


#### Permanent Storage with Docker

For the *permanent data storage* outside of the container, Docker offers the following two main options ([see Docker documentation](https://docs.docker.com/storage/) for details):

- *Volumes* (storage managed by the container platform, possibly mounted over a network)
- *Bind Mounts* (a normal folder in the local file system on the host running the container)

!!! danger "Whether volumes or bind mounts, ensure security and backups!"

    Both volumes and bind mounts can be used with the sedex client. You can choose the variant that fits better into your operating environment. Either way, your operations staff must ensure that only authorized users and applications have access to their portion of the data and that backups of the data are made regularly.


!!! note "Notes on the Permanent Data Storage"

    - The command line examples for Docker later in this documentation show the use of bind mounts. Experienced container administrators can easily adapt the examples to volumes.
    - In our examples the directory with the permanent data storage is named "sedex-data". Of course you can name this external bind mount directory or volume differently (for example, to indicate which sedex ID the data belongs to), but then you have to adapt the examples to your actual directory name. 
    - Other container platforms (Kubernetes, OpenShift, etc.) know specific concepts for persisted data volumes. Please refer to the corresponding documentation for your platform.




<a name="Mapped_Network_Ports"></a>
### Mapped Network Ports

!!! info "Network Port Mapping"

    Containers can publish network services to the outside via network ports. When creating a container, free external network ports must be mapped to the fixed internal network ports.

The following diagram once again shows the components of the sedex client Docker container. This time, however, we see which processes of the sedex client are running inside the container and under which network ports they can be reached internally.


![Overview of the sedex Client as a Docker Container](/assets/v6/sedex-client-container-volume-overview-2.png)

When creating the sedex client Docker container, externally available network port numbers will have to be mapped to the internal ones.
The examples in this documentation will show later how this network port mapping can be configured.



## The two phases of getting a sedex client Docker container up and running

The following diagram shows how to get a sedex client up and running. The start-up basically runs through two phases:

![The two phases of getting a sedex client Docker container up and running](/assets/v6/phase-1-and-phase-2.png)

[*Phase 1: Initial Configuration<br />*](./initial-configuration/initial_configuration_case_identification.md)
In this phase, the external permanent data storage ("sedex-data") is created and initially configured. This phase is executed once.

[*Phase 2: Run sedex Client<br />*](./run/run_with_docker.md)
In this phase, the sedex Client is created and connected to the prepared permanent data storage and run for normal operation. This stage is repeated several times in practice, because containers have a disposable character and therefore are more often thrown away and then recreated.




