# Initial Configuration using the prepared Init-Scripts

**The previous two pages show how the one-time initial configuration process of sedex-data can be executed manually using an init.conf file. In certain situations, it may be desirable to automate the one-time initial configuration process. To facilitate this automation, the sedex client container contains two utility init scripts. This page describes how to use these two init scripts.**
 


## Initial configuration with Container Init-Scripts instead of init.conf file


If the initial configuration process with manually creating an init.conf file described on the previous pages ([with CRID and OTP](initial_configuration_with_crid_and_otp.md), [with Certificate](initial_configuration_with_certificate.md) is not a feasible way, one of the two provided sedex Client container init-scripts may be able to help.

There is a separate init-script in the container for each of the two initialization cases a) and b) described in the [Basci Concepts](basic_concepts.md). The task of the init containers is to create the init.conf file from passed ENV parameters.

### a) Init Container for an existing participant

If you already have a participant with an existing sedex participant certificate (a P12 file).

Run the sedex Client container using environment-specific values for the following options:

- `--rm` - Automatically remove the container when it exits
- `/path/to/sedex-data` - The path to the host's "sedex-data" directory or volume (holding the persisted data)
- `SEDEX_ID` - environment variable holding the participant's sedex id
- `KEYSTORE` - environment variable holding the *content* of the keystore file (P12) in base64 encoded format
- `KEYSTORE_PW` - environment variable holding the password to the keystore file
- `OUTGOING_WEB_PROXY_HOST` - optional environment variable holding the http proxy host
- `OUTGOING_WEB_PROXY_PORT` - optional environment variable holding the http proxy port
- `OUTGOING_WEB_PROXY_USER` - optional environment variable holding the http proxy user
- `OUTGOING_WEB_PROXY_PASSWORD` - optional environment variable holding the http proxy password
- `WSPROXY_START` - optional environment variable that can be set to false, if web service proxy should not be started (default value is true)

<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->

Command to run the init container:

    $ docker run \
      --rm \
      --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
      --env SEDEX_ID=YOUR-SEDEX-ID \
      --env KEYSTORE=CONTENT-OF-YOUR-CERTIFICATE-FILE \
      --env KEYSTORE_PW=YOUR-PASSWORD \
      sedexch/sedex Client init-container-existing-cert.sh

**Note:** In a Linux console, the contents of the keystore file (P12) can be translated into the required base64 encoded format as follows:

    $ cat YOUR-CERTIFICATE-FILE.p12 | base64

Resulting output:

      MIIHdgIBAzCCBzwGCSqGSIb3DQEHAaCCBy0EggcpMIIHJTCCA+8GCSqGSIb3DQEHBqCCA+AwggPc
      AgEAMIID1QYJKoZIhvcNAQcBMBwGCiqGSIb3DQEMAQYwDgQIRYqcETINPCICAggAgIIDqJno8zFy
      [...]
      cDZCr7zStk1IBaD7WoHQqRmvlM9qJJrZsELaTflLcfo43GWGDiwX+OqTD0xo3J/EmVJ8fat/yKsM
      4lnSpDFaMCMGCSqGSIb3DQEJFTEWBBTPLjUs7MQ16yQIA6BqTg4C6uxj9jAzBgkqhkiG9w0BCRQx
      Jh4kAEMAbABpAGUAbgB0ACAAQwBlAHIAdABpAGYAaQBjAGEAdABlMDEwITAJBgUrDgMCGgUABBSF
      i3kuzz/2qGFiSHGVmHe+aHbm2wQIs0UZdpNO54oCAggA

**Note:** Since this content contains the private key of the sedex participant, it must be kept secret. This content and the corresponding password must not be visible to unauthorized persons at any time.


### b) initial configuration for a new participant

If you don't have an existing certificate but a *certificate request ID (CRID)* and a one time password (OTP).

Run the sedex Client container using environment-specific values for the following options:

- `--rm` - Automatically remove the container when it exits
- `/path/to/sedex-data` - The path to the host's "sedex-data" directory or volume (holding the persisted data)
- `SEDEX_ID` - environment variable holding the participant's sedex id
- `CRID` - environment variable holding the certificate request id (CRID)
- `OTP` - environment variable holding the one time password (OTP)
- `OUTGOING_WEB_PROXY_HOST` - optional environment variable holding the http proxy host
- `OUTGOING_WEB_PROXY_PORT` - optional environment variable holding the http proxy port
- `OUTGOING_WEB_PROXY_USER` - optional environment variable holding the http proxy user
- `OUTGOING_WEB_PROXY_PASSWORD` - optional environment variable holding the http proxy password
- `WSPROXY_START` - optional environment variable that can be set to false, if web service proxy should not be started (default value is true)

<!-- Start a new section to get Markdown to consider the following as code and not part of the list... -->

Command to run the init container:

    $ docker run \
      --rm \
      --mount type=bind,source=/path/to/sedex-data,destination=/sedex-data/ \
      --env SEDEX_ID=YOUR-SEDEX-ID \
      --env CRID=YOUR_CERTIFICATE_REQUEST_ID \
      --env OTP=YOUR-ONE-TIME-PASSWORD \
      sedexch/sedex Client init-container-new-cert.sh
