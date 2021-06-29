# Welcome to the Documentation for the official sedex-Client Docker Image

![sedex logo](/assets/sedex-logo.png)

## What is sedex-Client?

[**sedex**](http://www.sedex.ch) stands for *secure data exchange* and is a service of the [Swiss Federal Statistical Office (FSO)](https://www.bfs.admin.ch/bfs/en/home) that integrates business applications in the Swiss e-Government sector.

At its core, the **sedex platform** is a *message-oriented middleware* designed for secure asynchronous data exchange between business applications of different organizational units. In specific cases, synchronous data exchange (SOAP and RESTful web services) also takes place. The sedex platform is highly available (24/7).

The **sedex-client** is a software which is installed at the business applications and offers the interface between the business applications and the remote sedex server as a message hub. Further details about the internal structure of the sedex client can be found in the section [sedex System](./sedex-system/overview_sedex.md).


## Documentation

This documentation describes how to run a containerized version of the sedex-Client and is devided into the following top level sections:

- [Overview of the sedex System](./sedex-system/overview_sedex.md)
- [sedex-client Container Version 6](./client-container-v6/index.md)
- [Contact sedex Support](./contact.md)

## Docker image of the sedex-client

The Docker image of the sedex client is publicly available in different versions (tags) on the Docker Hub at [hub.docker.com/r/sedexch/sedex-client](https://hub.docker.com/r/sedexch/sedex-client)


## Alternative to the Docker Container: Classic Installation

If you do not want to run the sedex client as a container and instead want to install it yourself on your operating system, you can find the necessary installer and the associated instructions at [www.sedex.ch](http://www.sedex.ch).



