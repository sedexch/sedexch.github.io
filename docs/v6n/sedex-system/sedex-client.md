# sedex Client

## Core Functionalities of the sedex Client

The sedex Client provides the following core functionalities:

-	**Secure asynchronous messaging**
     - Secure transport of sensitive data 
     - Sender-side message encryption for targeted recipients; only the recipients can decrypt the message (known as end-to-end security)
     - Message content integrity check by means of digital signatures
     - Reliable transport for very large data files (1 GB or higher, compressed or uncompressed)
     - Sending/receiving of messages is nonrepudiable
     - Audit trail of the message exchange (envelope information only)

-	**sedex Web Service Proxy**
     - Offers local access to defined remote Web services
     - Uses the participant’s sedex certificate for the secure SSL/TLS channel to the target Web service
     - Enables the Web service provider to identify and authorize the caller


## Three Components of the sedex Client

![The sedex Client’s three components (processes)](/assets/v6/sedex-client-three-components.png)


The sedex Client consists of three separate components which are running inside the container:

1.	**sedex Controller**<br/>
    The process that controls and monitors the sedex Client’s two other processes (see the next two points). For example, the sedex Controller is able to control the sedex Client processes locally and/or remotely and to perform automatic updates of the sedex Client software.

2.	**sedex Adapter**<br/>
    The sedex Client’s main process, offering the functions that enable the secure and reliable exchange of messages on the sedex Client side. 

3.	**sedex Web Service Proxy**<br/>
    An optional process that offers a secure proxy for SOAP and RESTful Web services. It is not needed for the normal sedex message exchange.
