# sedex Client Docker Container (V6)


**This documentation describes how to install, configure and operate the current sedex Client version 6 on *container platforms* such as Docker, Kubernetes, OpenShift, etc. Before you start working with the sedex client container, it is important to take note of the following points.**


## Before Starting

### sedex is a Closed User Group

sedex is a closed user group for registered and authorized participants only.
Only subscribers with regulatory authorization and the appropriate credentials can use sedex.
Please contact sedexsupport@bfs.admin.ch if you are interested in subscribing to the sedex platform.

### Container or classic installation?

The sedex client can not only be operated as a container but can also be installed directly on numerous operating systems as a classic software installation.
The installer and the corresponding installation and user manual can be found at www.sedex.ch.


### Container and Documentation are Pre-Release

The official container image of the sedex Client and the accompanying documentation are currently still to be considered a pre-release and are in a public pilot phase.
It is strongly discouraged to run a critical productive sedex Client based on this image without the necessary knowledge.
Please contact [sedex support](http://www.sedex.ch) should you plan to use this container image or if you have any questions.


### Preparation required

The smooth operation of a sedex client is a demanding task. Please read the documentation carefully and plan your installation before starting the sedex client as a container for the first time.


### Help us improve
The sedex client as a container is a new offering.
Help us to improve the container image but also the documentation.
Therefore we are very interested in your feedback.
Please tell us what you like at sedexsupport@bfs.admin.ch.




## Attention! Do not miss these important technical points!


!!! danger "Before you start using sedex Client as a container, you need to be aware of the following important points!"



### One running instance only per participant

For a specific sedex participant (a specific sedex ID), a maximum of one sedex client may be running at any time.
Otherwise, problems such as lost messages or other malfunctions may result.
Therefore, you must absolutely ensure organizationally and operationally that no more than one sedex client is running for the same participant at any time.<br/>
This point has to be considered especially on automatically orchestrated container platforms like Kubernetes, OpenShift, etc.
Without specific configuration, these platforms tend to run multiple instances (for a short time) by default.
See page xxx for details.


### Permanent file storage for data volume required

Containers have, by principle, a disposable character. This means that any data stored in the container is also deleted with the container.
However, sedex needs data that survive the (possibly short) life cycle of a container and must be persisted permanently.
Examples for such important data to be stored are sedex messages, the participant certificate, etc.
In order to guarantee that this data can be permanently stored outside the container, it is imperative that the container has a reliable external data volume to which it has access.
See page xxx for details.



