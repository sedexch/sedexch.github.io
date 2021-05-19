# Introduction: sedex Client Docker Container (V6)

## sedex Client Software

The sedex Client is software that connects business and e-government applications with the secure data exchange platform sedex.
Typically, the sedex Client runs on the same hosts or platforms as these applications in Swiss municipalities, cantons and federal offices such as BFS, ZAS, Infostar, etc.
This documentation describes how to install, configure and operate the sedex Client on a container platform like Docker or Kubernetes. 

!!! info
    If you do not want to run the sedex Client as a container, then you are looking in the wrong place. You can also install the sedex Client yourself on your host under different operating systems. If you prefer such a classic non-container operation, then you can download the installer and the installation and user manual from www.sedex.ch.



## About the offical Container Image of the sedex Client

!!! warning
    The official container image of the sedex Client and the accompanying documentation are currently still to be considered a pre-release and are in a public pilot phase.
    
    It is strongly discouraged to run a critical productive sedex Client based on this image without the necessary knowledge.
    Please contact [sedex support](http://www.sedex.ch) should you plan to use this image or have any questions.


## Important Notes

 - **Private application:**
sedex is a closed user group for registered and authorized participants only.
If you are not such a registered participant, you cannot use sedex Client.

- **One running instance only:**
It is absolutely necessary that you prevent two or more sedex Clients from running simultaneously for the same participant.
In the worst case sedex messages could be lost.
Therefore it has to be ensured that only one sedex Client is running at a time.
This has to be considered especially when operating on automatic container orchestration platforms like Kubernetes.
See section [Run - with Kubernetes](run/kubernetes.md) for further details.

- **Permanent file storage for data volume required:**
There is important data that has to be stored permenently outside of the sedex Client Docker container.
Examples for such data are messages, receipts, local messaging state database, certificates, passwords etc.
If this data is not reliably stored/persisted outside the container, there is a risk of message loss and loss of access to the own message inbox.
See section [*Permanent Data Storage ("sedex-data")*](#Permanent_Data_Storage) for further details.

- **Feedback:**
We are very interested in your feedback on the Docker version of the sedex Client and this documentation.
Please tell us what you like and what you don't like at sedexsupport@bfs.admin.ch.


