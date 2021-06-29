# Optional Parameters of Initial Configuration

## Defining an outgoing HTTP-proxy in the init.conf

If an HTTP proxy is required to access the Internet, such a proxy server can optionally be entered already at the initial configuration phase in the file init.conf. This configuration is then included in the generated configuration of the sedex Client. 

Example:
```
File: /path/to/sedex-data/conf/init.conf
...
OUTGOING_WEB_PROXY_HOST=proxy.example.ch
OUTGOING_WEB_PROXY_PORT=8080
OUTGOING_WEB_PROXY_USER=example-user
OUTGOING_WEB_PROXY_PASSWORD=1234
```

!!! note "No username/password" 

    If no user name is required, the two optional parameters OUTGOING_WEB_PROXY_USER and OUTGOING_WEB_PROXY_PASSWORD can be omitted.

!!! info "Configure http proxy later on"

    An http proxy can also be configured after initial configuration at any time (see [Advanced Configuration of the sedex Client](../configuration/sedex-client-options.md)).
    

## Disabling the Web Service Proxy in the init.conf

If the functionality of the *sedex web service proxy* for secure access to defined web services is not used, it can already be deactivated at the very beginning during the initial configuration phase in the initial configuration file init.conf. This configuration is then included in the generated configuration of the sedex Client. 

Example:
```
File: /path/to/sedex-data/conf/init.conf
...
WSPROXY_START=false
```

!!! note "Default value is true"

    If this parameter is missing in init.conf a default value of "true" will be set in the configuration file.

!!! info "Re-enabling Web Service Proxy"

    If the usage of the web service proxy should be required later, it can also be activated after initial configuration at any time (see [Advanced Configuration of the sedex Client](../configuration/sedex-client-options.md)).

