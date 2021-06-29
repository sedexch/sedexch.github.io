# sedex Client

**The sedex Client is a software that sedex participants install in their data center near their business applications.
It is the endpoint of the sedex system and primarily allows the business applications to send and receive sedex messages.**

## Core Functionalities of the sedex Client

The sedex Client provides the following core functionalities:

### Secure asynchronous messaging

 - Secure transport of sensitive data 
 - Sender-side message encryption for targeted recipients; only the recipients can decrypt the message (known as end-to-end security)
 - Message content integrity check by means of digital signatures
 - Reliable transport for very large data files (1 GB or higher, compressed or uncompressed)
 - Sending/receiving of messages is nonrepudiable
 - Audit trail of the message exchange (envelope information only)

### sedex Web Service Proxy

 - Offers local access to defined remote Web services
 - Uses the participant’s sedex certificate for the secure SSL/TLS channel to the target Web service
 - Enables the Web service provider to identify and authorize the caller


## Three Components of the sedex Client


The sedex Client consists of three separate components (processes) which are running:

![The sedex Client’s three components (processes)](/assets/v6/sedex-client-three-components.png)


### 1. sedex Controller

The process that controls and monitors the sedex Client’s two other processes (see the next two points). For example, the sedex Controller is able to control the sedex Client processes locally and/or remotely and to perform automatic updates of the sedex Client software.

### 2. sedex Adapter

The sedex Client’s main process, offering the functions that enable the secure and reliable exchange of messages on the sedex Client side. 

### 3. sedex Web Service Proxy

An optional process that offers a secure proxy for SOAP and RESTful Web services. It is not needed for the normal sedex message exchange.



## Two operating models for the sedex client

The sedex participants have to operate their sedex client on their own infrastructure in their data centers.
Basically, the following two operating models are available to them:

### 1. sedex client as classic software installation

The sedex client is a Java-based application that can be installed on various operating systems (including Windows, Mac OS, Linux, AIX, etc.) using a software installer. This documentation does not cover a classic software installation. If you choose this model, you must follow the instructions in the *User and Installation Manual*, which can be found as a PDF in the download section of [www.sedex.ch](http://www.sedex.ch). There you will also find the latest sedex client installer.

### 2. sedex client as (Docker) container

The sedex client can also be run on a container platform such as Docker, Kubernetes, OpenShift, etc. For this purpose, sedex publishes an official container image on Docker Hub. If your organization has a stable container platform and the required knowledge, you can thus efficiently install and operate the sedex client as a container. This documentation covers the container operation model. If 
 you choose this model, 
 you must follow the instructions on this website carefully. 
