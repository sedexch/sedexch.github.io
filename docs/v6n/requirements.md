# Requirements

Before you start using the sedex client as a Docker container, it is important to ensure that you meet the following requirements.



## sedex Participant credentials

You can only start up the sedex-Client Docker container for a sedex participant if you are in ownership of that participant's credentials.

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
 
Docker Engine should be v17.06 or later. Older versions are not supported and the api syntax may differ from this manual.



## Memory / RAM

It is recommended to allocate at least 1GB memory for the sedex docker container.



## Persisted Data Folder

A permanent file store outside the container, which is mapped into the container as a volume or bind mount.
The data in this storage is sensitive and absolutely critical to protect.
This file store must therefore be

 - persistent,
 - protected from unauthorized access
 - and integrated into a backup.
