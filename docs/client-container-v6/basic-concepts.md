# Overview and Basic Concepts

**On this page you will get an overview of the sedex client as a Docker container and the basic concepts involved.
Before you start using the sedex Client as a Docker container, it is important to understand this page.**



## Overview of the sedex Client as a Docker Container

![Simplified overview of the sedex system](/assets/v6/sedex-client-container-volume-overview-1.png)




## Basic Concepts

The following basic concepts are important and thus explained below:

 1. Permanent Data Storage ("sedex-data")
 2. Initial Configuration Process
 3. Initial Configuration File ("init.conf")


<a name="Permanent_Data_Storage"></a>
### Permanent Data Storage ("sedex-data")

The sedex Client must be able to store certain data permanently outside of the container.
The following sedex specific data must survive the lifespan of a Docker container:

- private key to the sedex participant certificate
- configuration
- sedex messages
- messaging state
- log files
- etc.




For the *permanent data storage* outside of the container, Docker e.g. offers the following two options ([see Docker documentation](https://docs.docker.com/storage/) for details):

- *Volumes* (storage managed by the Container platform)
- *Bind Mounts* (local file system based storage on the host running the Container)


**Notes:**

 - The command line examples for Docker show the use of *bind mounts*. Experienced container administrators can easily adapt the examples to *volumes*.
 - In our examples the directory with the permanent data storage is named "sedex-data". Of course you can name this external directory or volume differently, but then you have to adapt the examples to your actual directory name. 
 - Other container platforms (Kubernetes, OpenShift, etc.) know specific concepts for persisted data volumes. Please refer to the corresponding documentation for your platform.


<a name="Initial_Configuration_Process"></a>
### Initial Configuration Process

If you are starting from scratch, i.e. you have never started a sedex Client as a Docker container for the participant before, then the sedex container must do an *initial configuration* of the external *permanent data storage* ("sedex-data") once (and only once) at the very first start.
During this one time configuration process, the container will automatically prepare the *permanent data storage* by creating the required directory structure and by creating the required configuration files.



<a name="Initial_Configuration_File"></a>
### Initial Configuration File ("init.conf")

In order to execute the *initial configuration process*, the container needs at its first start a prepared *initial configuration file* ("init.conf") as an input.
The format of this file is plain text and has to contain three *initial configuration parameters*.


The **content of the initial configuration file "init.conf"** depends on the type of credentials you have for the participant. You will find yourself in one of the following two initialization cases:

   - **Case a) Initial configuration for an existing sedex participant:**
  This means that a sedex Client has been running in another installation (Container and non-Container) for this participant before. Thus a sedex *participant-certificate* and a corresponding *password* already exist and must be reused.

   - **Case b) Initial configuration for a new sedex participant:**
  This means that there has no sedex Client been running for this participant before. Thus there is *no* participant-certificate yet but a so-called *certificate request id (CRID)* and a corresponding *one time password (OTP)*.

Follow the instructions for the "initial configuration* according to your actual init case:

   - [Case a) Initial configuration for an existing sedex participant](initial-configuration/initial_configuration_with_certificate.md)

   - [Case b) Initial configuration for a new sedex participant](initial-configuration/initial_configuration_with_crid_and_otp.md)
