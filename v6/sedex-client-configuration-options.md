## Custom Configuration options

**Note:**
Configuration changes become active after a *restart* of the container.
So restart the container to activate any changes made to the central configuration file "sedex-client-configuration.properties".


### Internet access via HTTP-Proxy
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

**Note:** Leave the user/password parameters blank if proxy server authentication is not required. Restart container to activate Changes.


### Timezone
The default timezone in the sedex-client Docker container is "Europe/Zurich" (i.e. local Swiss time).
If you have to set another timezone (e.g. UTC) then set the environment variable TZ by adding the following option to your container run statement:

    --env TZ=UTC   

### Disable WebSocket protocol
By default, the sedex-client uses the WebSocket protocol to reduce round trip times by actively notifying the client via events from the server.

In extremely rare cases, the WebSocket protocol can cause communication problems with network components.
If such a problem is detected, the use of WebSocket can be deactivated.
As a result, the client can only use so-called polling, which leads to longer round trip times, but solves the problems.

With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Add the following configuration parameter to the configuration file and save it:

    websocket.enabled=false
    
**Note:** Restart container to activate changes    

### Disable sedex Webservice Proxy
By default, the sedex-client is starting the sedex Webservice Proxy functionality. If not used, the sedex Webservice Proxy can be disabled in order to save some resources (CPU, memory).


With a text editor (e.g. "nano") open the central sedex-client configuration file named "sedex-client-configuration.properties" in the subdirectory "sedex-data/conf":

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Set the following configuration parameter in the configuration file to false and save it:

    wsproxy.start=false

**Note:** Restart container to activate changes

### Custom sedex Webservice Proxy endpoints

When developing or testing a Web service, it may be necessary to configure test instances of services.
The endpoint of a service provider behind the local Web service proxy endpoint can be configured in the following 
configuration file:

    $ nano /path/to/sedex-data/conf/sedex-client-configuration.properties

Adding the following line overrides the endpoint for the Web service *my-service*:

    wsproxy.services.my-service.target-url=https://www.example.com/path  

e.g. for UPIQueryService:
    
    wsproxy.services.UPIQueryService.target-url=https://wupi-test.zas.admin.ch/wupi_cc/UPIQueryService 

If no entry is made for a particular service, the default endpoint is used.
