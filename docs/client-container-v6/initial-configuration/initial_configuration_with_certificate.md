# Case a) Initial Configuration with P12-Certificate

**The following describes how the initial configuration of a new container installation is done for an existing participant, i.e. if you already have a sedex certificate (P12 file).**


!!! warning "Warning: Execute Initial Configuration only once "

    The initial configuration only has to be executed **just once before the very first start of the sedex Client container**.
    If an initial configuration is performed again later, misconfigurations can result and in the worst case important data such as private keys or sedex messages can be lost.


## Step 1: Create a sedex-data directory
Create a directory "sedex-data" on the local host or a network attached storage (NAS) where the sedex Client will store files that have to be persisted 
outside of the Docker container (ex. the sedex messaging interface directories). This directory will be used as a Docker bind mount.
```console
$ mkdir /path/to/sedex-data
```


## Step 2: Create a subdirectory conf for the configuration
Create a subdirectory inside of "sedex-data" directory. This directory will contain all configuration files for this sedex participant.
```console
$ mkdir /path/to/sedex-data/conf
```


## Step 3: Provide the initial configuration for an *existing* participant


### Provide the existing sedex certificate file (P12)
Copy the sedex keystore file (PKCS12/P12 format, containing the private key and certificate) into the "sedex-data/conf" subdirectory.
```console
$ cp /path/to/credentials/YOUR-CERTIFICATE-FILE.p12 /path/to/sedex-data/conf/
```

### Provide the file with the initial configuration parameters

With a text editor (e.g. "nano") create a plain text file named "init.conf" in the subdirectory "sedex-data/conf":
```console
$ nano /path/to/sedex-data/conf/init.conf
```

Add the following three configuration parameters (with values adjusted to your actual data) to your "init.conf" file and save it:

```console
SEDEX_ID=YOUR-SEDEX-ID
SEDEX_CERTIFICATE_FILENAME=YOUR-CERTIFICATE-FILE.p12
SEDEX_CERTIFICATE_PASSWORD=YOUR-PASSWORD
```
    
**Note:** After the first successful start of the sedex Client, the file init.conf will be **deleted automatically** as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section [Advanced Docker Options](advanced-docker-options)) or later manually be defined in the central configuration file of the sedex Client (see section [Advanced sedex Client Configuration](sedex Client-configuration-options)). 
