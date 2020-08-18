**Documentation for the current sedex-client V6 Docker Image on [docker hub](https://hub.docker.com/r/sedexch/sedex-client)**

***

# Custom sedex-Client Configuration Options

This page describes advanced aspects of Docker Container configuration. Most of these options are rarely needed.

## How to activate changes made to the sedex-Client Configuration

**Important:**

Configuration changes to the sedex-client become only active after a *restart* of the container.
So restart the container to activate any changes made to the central configuration file "sedex-client-configuration.properties".


## Internet access via HTTP-Proxy
If an HTTP proxy is required to access the Internet, such a proxy server can be entered in the central sedex-client configuration file.

Example:

    File: /path/to/sedex-data/conf/sedex-client-configuration.properties
    ...
    # Proxy settings: Set these values only if you reach the Internet in your network only through a proxy
    # Example: host=proxy.example.ch port=8080 user=example-user password=1234
    outgoing-web-proxy.host=proxy.example.ch
    outgoing-web-proxy.port=8080
    outgoing-web-proxy.user=example-user
    outgoing-web-proxy.password=1234
    ...

**Note:** Leave the user/password parameters blank if proxy server authentication is not required. Restart container to activate changes.


## Disable WebSocket protocol
By default, the sedex-client uses the WebSocket protocol to reduce round trip times by actively notifying the client using events sent from the server to the client.

In extremely rare cases (approx. 1% of all clients), the WebSocket protocol can cause communication problems with network components on the client side.
If such a problem is detected, the use of WebSocket can manually be deactivated.
As a result, the client can only use the so-called *polling* mode, which leads to longer message round trip times (about 10 minutes instead of a few seconds), but the sedex-client can still offer the basic functionality without websocket if required. **Note:** Normally the web socket communication should *not* be disabled. 

With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Add the following configuration parameter to the configuration file and save it:

    websocket.enabled=false
    
**Note:** Restart container to activate changes    


## Disable sedex Webservice Proxy
By default, the sedex-client is starting the sedex Webservice Proxy functionality. If not used, the sedex Webservice Proxy can be disabled in order to save some resources (CPU, memory).

With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Set the following configuration parameter in the configuration file to false and save it:

    wsproxy.start=false

**Note:** Restart container to activate changes


## Custom sedex Webservice Proxy endpoints

Normally the definitions of the web services offered by the sedex Web Service Proxy are automatically provisioned and require no manual configuration. But if you are developing or testing a new web service that should be offered via sedex Web Service Proxy, it may be necessary to manually configure test instances of services.
For this purpose the backend endpoint of a web service proxy can be overridden locally in the central sedex-client configuration file:

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Adding the following line overrides the endpoint for the Web service with name *my-service* (adapt name to your service):

    wsproxy.services.my-service.target-url=https://www.example.com/path  

e.g. override the endpoint of the web service UPIQueryService:
    
    wsproxy.services.UPIQueryService.target-url=https://wupi-test.zas.admin.ch/wupi_cc/UPIQueryService 

If no entry is made for a particular service, the default endpoint defined by sedex is used.

**Note:**
If the custom endpoint of a Web service requires a custom SSL/TLS-certificate not contained in the standard sedex truststore, you can override the default truststore as follows.

Copy your custom SSL/TLS-certificate (e.g. upiquery-test.cer) into the local certificate folder of your sedex-client:

     $ cp /path/to/yourTLSCert.cer /path/to/sedex-data/conf/certificates/upiquery-test.cer

Add the following configuration line to the central sedex-client configuration file referencing your certificate file:
    
     wsproxy.services.UPIQueryService.target-tls-certificate-filename=upiquery-test.cer  

