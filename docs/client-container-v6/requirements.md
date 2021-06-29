# Requirements

**Before you start using the sedex Client as a container, it is important to ensure that you meet the following requirements.**



## sedex Participant Credentials

You can only start up the sedex Client container for a sedex participant if you are in ownership of that **participant's credentials**.

Two different sets of credentials are possible:

 **1. for a new participant:**
 
 - sedex ID
 - Certificate Request ID (CRID)
 - One Time Password

**2. for an existing subscriber:**

 - sedex-ID
 - Participant certificate (P12 file)
 - Password to the P12 file



## Docker Version
 
Docker Engine v17.06 or later.


## Memory / RAM

1GB memory / RAM for the sedex docker container.

!!! tip
    On orchestration platforms (Kubernetes, OpenShift, etc.): If additional containers will be running along the sedex Client container (e.g. side car proxies of a service mesh), don't forget to add their memory requirement when allocating memory for the Pod.



## Persisted Data Folder / Persisted Data Volume

A permanent file store outside the container, which is mapped into the container as a volume or bind mount.


!!! warning
    The data in this storage is sensitive, critical and has to be fully protected.

    This file store must therefore be:

      - persistent,
      - protected from unauthorized access
      - and integrated into a backup.


## Maximum of one instance at any time

Due to the architecture of the sedex system, a maximum of one sedex Client may be running for a participant at any time. This must also be ensured when operating the sedex Client as a container.

!!! warning
    This must also be ensured on container platforms while the orchestrator moves or updates the sedex Client container. This can be ensured on Kubernetes e.g. by using a `StatefullSet` or a `Deployment` with `spec.strategy.type=Recreate`. 