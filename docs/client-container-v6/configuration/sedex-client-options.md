# Advanced Configuration of the sedex Client

**In the following, you will find how you can change configuration parameters for the sedex Client and which parameters are available for this purpose.**


## Default configuration

During the very first start of the sedex Client container, the initial configuration takes place.
This creates a default configuration, which can be used unchanged in most cases.
Setting custom configuration parameters should rarely be necessary, but can be done as described on this page.


## How to customize the Configuration of the sedex Client

The sedex client can be configured by setting configuration parameters in the form of specific key-value pairs. These configurations parameters can be set in two ways:

1. in the sedex Client's central configuration file

2. as environment variables of the Docker container

!!! note "Environment variables of higher priority"

    If the same key is defined both in the central configuration file and as an environment variable, the environment variable value has a higher priority and will be used.


### Setting custom Parameters in the client configuration file ("sedex-lient-configuration.properties)

In order to set a custom configuration parameter via the central sedex Client configuration file, the relevant key in the file must be set to the desired value as follows: 

1. With a text editor (e.g. "nano" or "notepad") open the central sedex Client configuration file named "sedex-lient-configuration.properties" located in the subdirectory "sedex-data/conf":

       ``` console
       $ nano /path/to/sedex-data/conf/sedex Client-configuration.properties
       ```

2. Add the necessary configuration parameter(s) to the configuration file and save it.

3. Restart the sedex Client container in order to activate the new configuration


### Setting custom Parameters as Environment Variables


## Available Parameters for custom sedex Client Configuration