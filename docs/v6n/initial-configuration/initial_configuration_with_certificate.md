# Initial Configuration

!!! warning

    The initial preparation steps only have to be executed **just once before the very first start of the sedex-client**.


#### Step 1: Create a sedex-data directory
Create a directory "sedex-data" on the local host or a network attached storage (NAS) where the sedex-client will store files that have to be persisted 
outside of the Docker container (ex. the sedex messaging interface directories). This directory will be used as a Docker bind mount.
```console
$ mkdir /path/to/sedex-data
```


#### Step 2: Create a subdirectory conf for the configuration
Create a subdirectory inside of "sedex-data" directory. This directory will contain all configuration files for this sedex participant.
```console
$ mkdir /path/to/sedex-data/conf
```


#### Step 3a: Provide the initial configuration for an *existing* participant

!!! note

    Execute this step only, if a sedex participant-certificate and a corresponding password already exist.
    For new participants skip to Step 3b.

##### Provide the existing sedex certificate file (P12)
Copy the sedex keystore file (PKCS12/P12 format, containing the private key and certificate) into the "sedex-data/conf" subdirectory.
```console
$ cp /path/to/credentials/YOUR-CERTIFICATE-FILE.p12 /path/to/sedex-data/conf/
```

##### Provide the file with the initial configuration parameters

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
    
**Note:** After the first successful start of the sedex-client, the file init.conf will be **deleted automatically** as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section [Advanced Docker Options](advanced-docker-options)) or later manually be defined in the central configuration file of the sedex client (see section [Advanced sedex Client Configuration](sedex-client-configuration-options)). 

    
#### Step 3b: Provide the initial configuration for a *new* participant

!!! note

    Execute this step only for *new* sedex participant with no existing certificate but instead with a *certificate request id (CRID)* and a *one time password (OTP)*. 
    For new participants follow Step 3a.

##### Provide the file with the initial configuration parameters

With a text editor (e.g. "nano") create a plain text file named "init.conf" in the subdirectory "sedex-data/conf":

```console
$ nano /path/to/sedex-data/conf/init.conf
```

Add the following three configuration parameters (with values adjusted to your actual data) to your "init.conf" file and save it:

```console
SEDEX_ID=YOUR-SEDEX-ID
SEDEX_CRID=YOUR_CERTIFICATE_REQUEST_ID
SEDEX_OTP=YOUR-ONE-TIME-PASSWORD
```

**Note:** After the first successful start of the sedex-client, the file init.conf will be **deleted automatically** as its content will be moved to the created sedex configuration files.

**Note:** If an HTTP proxy is required for access to the Internet, such a proxy server can optionally already be defined in the init.conf file (see section [Advanced Docker Options](advanced-docker-options)) or later manually be defined in the central configuration file of the sedex client (see section [Advanced sedex Client Configuration](sedex-client-configuration-options)). 
