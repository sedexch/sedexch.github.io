# Custom Configuration of the sedex Client

**In the following, you will find how you can change configuration parameters for the sedex Client and which parameters are available for this purpose.**


## Default configuration

During the very first start of the sedex Client container, the initial configuration takes place.
This creates a default configuration, which can be used unchanged in most cases.
Setting custom configuration parameters should rarely be necessary, but can be done as described on this page.


## Custom Configuration

The sedex client can be configured by setting configuration parameters in the form of specific key-value pairs. These configurations parameters can be set in two ways:

1. in the sedex Client's central configuration file

2. as environment variables of the Docker container

!!! note "Environment variables of higher priority"

    If the same key is defined both in the central configuration file and as an environment variable, the environment variable value has a higher priority and will be used. This means that environment variables override the configuration file. 

Both configuration types are described in more detail in the following.


### Via the central sedex Client configuration file

Custom configuration parameters can be set in the central sedex Client configuration file ("sedex-client-configuration.properties).

Specific key-value pairs can be set using the configuration file as follows: 

1. With a text editor (e.g. "nano" or "notepad") open the central sedex Client configuration file named "sedex-lient-configuration.properties" located in the subdirectory "sedex-data/conf":

       ``` console
       $ nano /path/to/sedex-data/conf/sedex Client-configuration.properties
       ```

2. Add the necessary configuration parameter(s) to the configuration file and save it.

       ``` console
       key_name_1=value_1
       key_name_2=value_2
       ```

3. Restart the sedex Client container in order to activate the new configuration


### Via Docker Container Environment Variables

Custom configuration parameters can be set via environment variables of the Docker container.

Specific key-value pairs can be set using environment variables as follows: 

1. Stop & Delete the current sedex Client container

       ``` console
       $ docker stop sedex-client
       $ docker rm sedex-client
       ```

2. Run the sedex Client container and add the necessary configuration parameter(s) as environment variables

       ```console
       $ docker pull sedexch/sedex Client:latest
       
       $ docker run \
         --name sedex-client \
         --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
         --publish YOUR_MONITORING_PORT:8000 \
         --publish YOUR_WS_PROXY_HTTP_PORT:8080 \
         --publish YOUR_WS_PROXY_HTTPS_PORT:8443 \
         --stop-timeout 65 \
         --restart unless-stopped \
         -d \
         --env KEY_NAME_1=VALUE_1 \
         --env KEY_NAME_2=VALUE_2 \
         sedexch/sedex-client:latest
       ```

## Available Configuration Parameters for the sedex Client

The following configuration parameters for the sedex client are available. 


### Internet access via HTTP-Proxy

If an HTTP proxy is required to access the Internet, such a proxy server can be added as a custom configuration

    # Proxy settings: Set these values only if you reach the Internet in your network only through a proxy
    # Example: host=proxy.example.ch port=8080 user=example-user password=1234
    outgoing-web-proxy.host=proxy.example.ch
    outgoing-web-proxy.port=8080
    outgoing-web-proxy.user=example-user
    outgoing-web-proxy.password=1234

!!! note "You have no username/password?"

    Leave the user/password parameters blank if proxy server authentication is not required.



### Disable WebSocket protocol

By default, the sedex Client uses the WebSocket protocol to reduce round trip times by actively notifying the client using events sent from the server to the client.

In extremely rare cases (approx. 1% of all clients), the WebSocket protocol can cause communication problems with network components on the client side.
If such a problem is detected, the use of WebSocket can manually be deactivated.
As a result, the client can only use the so-called *polling* mode, which leads to longer message round trip times (about 10 minutes instead of a few seconds), but the sedex Client can still offer the basic functionality without websocket if required.

If disbaling WebSocket is required, this can be achieved by adding the following custom configuration

    websocket.enabled=false


!!! warning "Disable Websocket communication only if it is really necessary."

    Normally the web socket communication should *not* be disabled, as this has a negative impact on sedex's performance.
 
 

### Disable sedex Webservice Proxy

By default, the sedex Client is starting the sedex Webservice Proxy functionality. If not used, the sedex Webservice Proxy can be disabled in order to save some resources (CPU, memory).

Disable the sedex Webservice Proxy by adding the following custom configuration

    wsproxy.start=false



### Custom sedex Webservice Proxy endpoints

Normally the definitions of the web services offered by the sedex Web Service Proxy are automatically provisioned and require no manual configuration. But if you are developing or testing a new web service that should be offered via sedex Web Service Proxy, it may be necessary to manually configure test instances of services.

Adding the following configuration parameter overrides the endpoint for the Web service with name *my-service* (adapt name to your service):

    wsproxy.services.my-service.target-url=https://www.example.com/path  

Example: override the endpoint of the web service UPIQueryService:
    
    wsproxy.services.UPIQueryService.target-url=https://wupi-test.zas.admin.ch/wupi_cc/UPIQueryService 

If no entry is made for a particular service, the default endpoint defined by sedex is used.

**Note:**
If the custom endpoint of a Web service requires a custom SSL/TLS-certificate not contained in the standard sedex truststore, you can override the default truststore as follows.

Copy your custom SSL/TLS-certificate (e.g. upiquery-test.cer) into the local certificate folder of your sedex Client:

     $ cp /path/to/yourTLSCert.cer /path/to/sedex-data/conf/certificates/upiquery-test.cer

Add the following configuration parameter referencing your certificate file:
    
     wsproxy.services.UPIQueryService.target-tls-certificate-filename=upiquery-test.cer  
