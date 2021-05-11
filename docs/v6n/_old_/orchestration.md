# Kubernetes & Co.

The sedex client Docker container can be run on a Kubernetes-based orchestration platform. The following points must be taken into account.

## Memory Allocation
It is recommended to allocate at least 1GB memory for the sedex docker container. If additional containers will run within a Kubernetes Pod (e.g. side car proxies of a service mesh), their memory requirements must be added and more memory must be allocated to the Pod accordingly.

## Initial configuration with Init Containers instead of init.conf file

Container orchestration platforms such as Kubernetes know the concept of so-called *Init Containers*. These utility containers are started before the actual container is started, with the task of performing initialization tasks.

If the initial configuration described above (with manually creating an init.conf file) is not a feasible way, one of the two provided init containers from sedex may be able to help.

There is a separate init container for each of the two initialization cases a) and b) described in the [Basic Usage](index.md). The task of the init containers is to create the init.conf file from passed ENV parameters.

### a) Init Container for an existing participant

If you already have a participant with an existing sedex participant certificate (a P12 file).

Run the sedex-client container using environment-specific values for the following options:

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
      sedexch/sedex-client init-container-existing-cert.sh

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

Run the sedex-client container using environment-specific values for the following options:

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
      sedexch/sedex-client init-container-new-cert.sh


## Ensure a maximum of one client instance 

Due to the architecture of the sedex system, **a maximum of one sedex client may be running for a participant at any time**. This must also be ensured when operating the sedex client as a container.

On an orchestration platform for each container can be defined how many instances should run. E.g. Kubernetes calls this the number of *"replicas"*.

An orchestration platform can move containers to another node at any time. Usually this is done by starting a container on the new node before the old one is deactivated. This strategy may make sense for most web applications. However, when applied to the sedex client, this would result in two sedex clients running for a certain period of time. This can be prevented by setting the strategy to 
be used. Kubernetes calls the strategy to be chosen for sedex "Recreate".

The following is an excerpt from a Kubernetes deployment for the sedex client:

        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: sedex-client-deployment
          labels:
            app: sedexch/sedex-client/1-123455-1
        spec:
          replicas: 1
          strategy:
            type: Recreate
          selector:
            matchLabels:
              app: sedexch/sedex-client/1-123455-1
          template:
            metadata:
              labels:
                app: sedexch/sedex-client/1-123455-1
            spec:
              containers:
              - name: sedexch/sedex-client
                image: sedexch/sedex-client:6.0.0
        [...]
