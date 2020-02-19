**Documentation for the current sedex-client V6 Docker Image on [docker hub](https://hub.docker.com/r/sedexch/sedex-client)**

***

# Web Service Proxy

This page describes the web service proxy functionality of the sedex client. If you only use asynchronous sedex messaging, this page is not relevant for you. 


## Purpose of the Web Service Proxy

The web service proxy (WS-Proxy) is an add-on component of the sedex client offering local access to defined remote Web services. For the business application it looks as if the sedex web service proxy would offer the web service. In fact, the sedex WS-Proxy is only an intermediary between the application and the actual web service.

![sedex logo](https://raw.githubusercontent.com/sedexdev/sedexdev.github.io/master/static_files/assets/images/v6/wsp.png)

To establish a secure SSL/TLS connection to the actual web service, the WS-Proxy uses the participant's sedex certificate. This allows the service provider to unambiguously identify the caller and to check whether this caller is authorized to use the service or not.


## Activating Web Service Proxy

Whether the WS-Proxy is active or not is controlled by a property in the central client configuration. The following excerpt from a sample configuration shows an activated WS-Proxy:

       wsproxy.start=true 

    
## Checking if Web service proxy is active

On the monitoring page of a running sedex client, you can check whether the WS-Proxy is active. If at least some of the “wsp” elements shown below are contained, the web service proxy is active. If there are no entries starting with the prefix “wsp-“, then the WS-Proxy is not active.

        [...]
        wsp-https-port=8443
        wsp-https-tls-certificate-subject=CN=sedex-wsproxy, O=sedex (www.sedex.ch)
        wsp-https-tls-certificate-issuer=CN=sedex-wsproxy, O=sedex (www.sedex.ch)
        wsp-https-tls-certificate-create-date=2019-12-04T12:43:54
        wsp-https-tls-certificate-expiry-date=2024-12-02T12:43:54
        wsp-https-tls-certificate-alternative-names=wsproxy,localhost
        wsp-https-tls-certificate-is-valid=true
        wsp-http-disabled=false
        wsp-http-port=8080
        wsp-uptime=00:05:55
        wsp-users-allow-anonymous=true
        wsp-version=WsProxy 6.0.0
        wsp-webservice=/wsproxy/services/CheckSedexWebService  [protocols:http,https] 
                        [users:anonymous,authorized]
        wsp-webservice=/wsproxy/services/PartnerServices  [protocols:https]
                        [users:authorized]
        wsp-webservice=/wsproxy/services/sedexExternalAuthorisationService 
                        [protocols:http,https] [users:anonymous,authorized]
        wsp-webservice=/wsproxy/services/ZRWebserv/getNNSS  [protocols:https]
                        [users:authorized]
        wsp-webservice-definitions-updated-at=2019-12-04T13:15:43
        wsp-webservice-definitions-expires-at=2019-12-05T13:15:43
        wsp-webservice-definitions-update-status=OK
        wsp-webservice-definitions-update-info=Updated successfully
        wsp-webservice-definitions-valid=true
        wsp-webservice-last-call=controller-uptime=00:06:00 
        [...]        


## Configuring Web Service Proxy

The WS proxy is configured via the main configuration file of the sedex client:

        <sedex_home>/conf/sedex-client-configuration.properties

In this section, the configuration parameters specific to the WS-Proxy are described.


### Enabling & disabling Web Service Proxy

If the WS-Proxy functionality is not required, it can be switched off using the following configuration setting:

        wsproxy.start=false
 
        
### Disabling Anonymous Access

Regarding access control there are two basic types of web services:

1.	**Unrestricted webservices** - allowing anonymous access\
These are web services that are open for everybody with network access to the WS-Proxy. This means that a business application can call the web service via WS-Proxy without indicating a username and password. One says that such a caller is anonymous to the WS-Proxy.

2.	**Restricted webservices** - requiring an authenticated and authorized user\
These are web services that are restricted to a group of known and authorized users. This means that a business application can only call the web service together with a valid username & password combination. One says that such a caller is authenticated by the WS-Proxy. Once authenticated, a user can be authorized, i.e. it can be checked whether he has the right to use the service or not.

The web service definitions provided by sedex determine which web service may be called anonymously and which not.

If it is desired for security reasons to completely deactivate anonymous access to webservices, this can be achieved with the following setting:

        wsproxy.users.allowAnonymous=false

**Note:** If anonymous access is switched off, all accesses from business applications to web services must be made via the https protocol and with user name & password.


### Local Network Endpoints Configuration

The sedex WS-Proxy offers two separate network endpoints where business applications can send their web service requests to:

1.	**Endpoint for access using http (unsecured)**\
Web service requests and responses are transmitted using the http protocol . This means that the communication between the business application and the WS-Proxy is open/unsecured. Up to version 5.3 the sedex WS-Proxy only offered this endpoint.

2.	**Endpoint for access using https (secured)**\
Web service requests and responses are transmitted using the https protocol . This means that the communication between the business application and the WS-Proxy is cryptographically secured using SSL/TLS  . As of version 6.0, the sedex WS proxy also offers this endpoint. This endpoint should be preferred.

**Note:** The web service definitions provided by sedex determine which web service may be called over http and which not. See section 11.8.2 on how to check which web services are available over http and which are only available over https.

**Note:** Even when the business application accesses the WS-Proxy via unencrypted http, the connection between the WS-Proxy and the effective web service is always secured and encrypted via https.
The properties allowing the configuration of the http and https endpoints are described below.


**Unsecured http port**

The unsecured http port number on the inside of the container is 8080. You can publish/map this port to the desired port number on the outside using your container platform.  **Note:** You can omit this port publication/mapping if the WS-Proxy will not be used or will only be used via https.

      --publish YOUR_WS_PROXY_HTTP_PORT:8080


**Secured https port**

The secured https port number on the inside of the container is 8443. You can publish/map this port to the desired port number on the outside using your container platform.

      --publish YOUR_WS_PROXY_HTTPS_PORT:8443


### SSL/TLS Certificate Configuration

As of version 6.0, the WS-Proxy offers the possibility of a secure communication between the business application and the WS-Proxy using https protocol. The https protocol is based on SSL/TLS standard for the cryptographic protection of the communication channel. This requires a special SSL/TLS certificate to be installed and configured in the WS-Proxy.

Basically two types of SSL/TLS certificates can be used:

1.	**Generated by the sedex client (self-signed certificate)**\
If the client is started and no SSL/TLS certificate is configured, the client automatically generates a new self-signed certificate.

2.	**Existing certificate provided by the operator (may be an official SSL/TLS certificate)**\
The operator of a sedex client can obtain and use an official SSL/TLS certificate from a certificate provider and configure this in the sedex client.

**Note:** A self-signed certificate is a certificate that is not signed by a public and generally trusted certificate authority (CA). These certificates are easy to make and are free. A disadvantage of self-signed certificates is that they are not automatically trusted. This means that such certificates must always be added manually to the trust store of a user – here the business application 
calling the WS-Proxy.


**Using the self-signed certificate generated by the sedex client**

If the sedex client does not find an SSL/TLS certificate for the WS proxy at startup, it automatically generates and configures a self-signed certificate.

Notes regarding the self-signed certificate:

-	The self-signed certificate is valid for 5 years. You can find the expiration date on the monitoring page of the sedex client.

-	If you want to recreate the certificate, simply delete the path to the certificate from the property in the configuration file:\

            wsproxy.https.key-store-file=

-	The generated certificate is valid for all host names that the sedex client could automatically detect. The WS-Proxy writes these host names to the log file during the creation of the self-signed certificate. Alternately, you can open and examine the certificate.

-	To create a self-signed certificate with an additional host name of your choice do the following steps: 

        1. add the parameter
        
                wsproxy.https.hostname=your-host-name
                
        2. Delete the value of the parameter
        
                wsproxy.https.key-store-file=
                
        3. Let the the certificate be recreated by restarting the sedex client


-	The generated certificate is valid for all host names that the sedex client could automatically detect.

-	The keystore file generated for the self-signed certificate is in P12 format and is stored under the following path:

            sedex-data/conf/certificates

-	Additionally a CER file containing the public certificate is created. You can pass this file on to the developers or operators of the business application so that they can include the self-signed certificate in their trust store.


**Using an existing certificate provided by the operator**

If you already have a valid SSL/TLS certificate, the WS proxy can be configured to use it for its https endpoint. In order to use your own certificate, it must be in one of the following formats:
-	PKCS#12 (p12 file)
-	Java Key Store (jks file)

**Note:** The keystore provided must contain both the private key and the certificate under the same alias name.  

**Tip:** The freely available Java based tool “KeyStore Explorer” (https://keystore-explorer.org) allows the comfortable editing of Keystore files. Among other things, you can create a keystore from private keys and certificates in separate files, which can then be used by WS-Proxy.

Once you have your own SSL/TLS certificate in a supported format, you can install and configure it in the sedex client as follows:

1.	Copy your keystore file (e.g. my-ssl-keystore.p12) into the sedex-client configuration folder for certificates:\
sedex-data/conf/certificates/my-ssl-keystore.p12

2.	Configure your keystore file in the sedex client’s main configuration file:

        -	*Path to the keystore file*\  

            wsproxy.https.key-store-file=my-ssl-keystore.p12
            
        -	*Password with which the keystore file is secured*\
        
            wsproxy.https.key-store-password=myKeystorePassword

        -	*Alias name of the key to use*\
        A keystore file may contain one or many private keys. To select the correct key, its alias name must be specified. If you don't know the alias name, you can examine the content of the keystore with a suitable tool (e.g. with the KeyStore Explorer mentioned above).

            wsproxy.https.key-alias=my-key-alias

        -	*Password for accessing the key to use*\
        Within a keystore, a separate password can be set again for each key. If no separate password has been set for the key, enter the password for the keystore again here.

            wsproxy.https.key-password=myPrivatePasswordForTheKey

3.	Restart the sedex client and check the log file of WS-Proxy for errors.


## Administering Web Service Proxy Users

As of sedex client version 6.0, the sedex WS-Proxy supports the feature to restrict access to the web services it offers. This means that a business application can only call the web service if it has a valid username & password combination. The allowed usernames and their passwords are contained in a separate WS-Proxy user configuration file.

This sections describes the content and the basic administration of the WS-Proxy user configuration file.


### The WS-Proxy User Configuration File

Usernames and passwords are configured in the following user configuration file:

        sedex-data/conf/sedex-wsproxy-users.properties

This is an excerpt from the configuration file showing two configured users:

        # File: sedex-wsproxy-users.properties
        […]
        example-app-1=myNewAndSecurePassword,3-CH-55438
        example-app-2={bcrypt}$2a$10$f/du7KAWE0xVu.a9DrRKN.fBxg[…]1wldBVam,1-CH-2874423
        […]

The format of the entries in the configuration file is as follows:

        user_name=password,sedex-id

The elements of an entry (i.e. line of the configuration file) in detail are:
-	*user_name:*	A freely definable username that represents your application calling the
 		sedex webservice proxy. Allowed characters: a-z, A-Z, 0-9, -, _
-	*password:*	The password of the user. Allowed characters: a-z, A-Z, 0-9, -, _
-	*sedex-id:*	The associated sedex ID is that of the physical or logical sedex
		participant for whom the remote Web service is called.
		The specific sedex-id to be used is provided either by your
		sedex domain administrator or by FSO’s sedex customer support.

**Please note:**
-	Changes to the users file only become active after a restart of the sedex client.
-	New passwords can be specified in plain text.
-	For security reasons, plain text passwords are automatically encrypted during a restart of the sedex client.
-	Encrypted passwords will look like: {bcrypt}$2a$10$f/du7KAWE0xVu.a9DrRKN.fBxgk98zwNAUWPOa1nGgot01wldBVam
-	A once encrypted and forgotten password has to be reset by deleting the old encrypted password (including the {bcrypt} part} and replacing it by a new password in plain text.


### Add a new user

If a new business application (e.g. named «myApp») needs access to one or more web services via sedex WS-Proxy, a new user representing this application has to be added to the user configuration file:

1.	Open the user configuration file in a text editor

2.	Add a line for the new user (e.g. at the end of the file):

            myApp=aRandomPassword,sedexIdOfThisApplication

3.	Save the configuration file

4.	Restart sedex client

5.	Check the log file of WS-Proxy for errors or warnings


### Change the password of a user

The password of a user can be changed as follows:

1.	Open the user configuration file in a text editor

2.	For the intended user change the password element as shown below.\
\
    Before (with the old encrypted password):

           myApp={bcrypt}$2a$10$f/du7KAWE0xVu.a9DrRKN.fBxg[…]1wldBVam,1-CH-3322

         After (with the new plain password):

            myApp=aNewRandomPassword,1-CH-3322

3.	Save the configuration file

4.	Restart sedex client

5.	Check the log file of WS-Proxy for errors.\
You should see a line indicating that a previously plain password has been encrypted:


        
        
## Provided Web Services

### Automatic Provisioning of Webservice Definitions

The web service definitions are automatically retrieved from the sedex server by the sedex client and updated regularly.

To ensure that the WS proxy still functions even if the sedex server cannot be reached, the web service definitions are cached locally by the sedex client. This cache can be used for a maximum of one week. If the list of Web service definitions cannot be updated for more than a week, the Web service proxy stops its service with an error message in the log and the Web service responses.

The information as to whether the web service definitions can be updated and how long they are valid can be taken from the client’s monitoring page:

        […]
        wsp-webservice-definitions-updated-at=2019-12-04T13:15:43
        wsp-webservice-definitions-expires-at=2019-12-05T13:15:43
        wsp-webservice-definitions-update-status=OK
        wsp-webservice-definitions-update-info=Updated successfully
        wsp-webservice-definitions-valid=true
        […]


###	List of provided Web Services

The list of web services offered on a WS-Proxy differs from sedex participant to sedex participant and depends on the rights configured on the sedex server for this participant. 

The list of Web services currently available for a specific client can be viewed via its monitoring page. The following excerpt shows four defined webservices:

        […]
        wsp-webservice=/wsproxy/services/CheckSedexWebService  [protocols:http,https] [users:anonymous,authorized]
        wsp-webservice=/wsproxy/services/PartnerServices  [protocols:https] [users:authorized]
        wsp-webservice=/wsproxy/services/sedexExternalAuthorisationService [protocols:http,https] [users:anonymous,authorized]
        wsp-webservice=/wsproxy/services/ZRWebserv/getNNSS  [protocols:https] [users:authorized]
        […]


The elements of a web service definition are:

-	URL Path on the WS-Proxy
Path of the Web service on the WS proxy. The complete URL of the Web service is therefore derived from the protocol, host name, port, and this path as in the following example:
https://myWsProxyHost:8443/wsproxy/services/CheckSedexWebService  

-	Supported protocols
Specifies which protocols can be used to call the Web service via WS proxy. Possible are http (unsecured) and https (secured).

-	Access control
Specifies whether the web service can be called anonymously via WS proxy or exclusively with user name & password. Possible are anonymous and authorized.




