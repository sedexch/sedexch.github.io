# Versions and Changes

**This page explains the structure of version numbers and how version numbers are mapped to Docker container tags of images. The major changes of each version are listed.**

## About Version Numbers

The *full version number* of a sedex container image consists of two composite version numbers of the contained compoents, as shown in the following table:


| Component | Version number |
|---|---|
| full version number for the complete container image | `6.0.6_container-0.99-beta` |
| version of the *sedex client* contained in the container | `6.0.6` |
| version of the *container logic* | `container-0.99-beta` |


## About Docker Hub Tags of an Image 

Each sedex container image on Docker Hub is tagged with multiple tags representing the different version numbers:

| Tag | Meaning of the Tag |
|---|---|
| `6.0.6_container-0.99-beta` | The full version tag references the sedex container image with the specific client and the specific container version. |
| `6.0.6` | The version tag containing only the sedex Client version references the most current sedex container image with the specific client. |
| `latest` | The version tag `latest` references the most current sedex container image with the most current client. |



## Versions and Changes

### Version/Tag `6.0.6_container-0.99-beta` (2021-07-12)


#### New sedex Client `6.0.6`

This release does not introduce any new functionality, but resolves issues and improves some operational aspects of the sedex client. Includes changes from version 5.0.5 and 5.0.6.

- **Fix:** During each startup, the sedex client automatically checks that it has write per-mission on all required folders and files and aborts the startup if the check is not com-pletely successful. This check also includes the messaging interface folders (outbox, inbox, receipts, processed). If a business application or an administrator creates files in one of these folders using a different 
user, the sedex client may not have write per-mission on these externally created files and the check will fail during the next startup of the sedex client. This version fixes the problem by checking only the folders for ex-isting write permissions, but not the individual files.
- **Fix:** In rare situations, problems could occur under Windows when receiving sedex messages if non-ASCII special characters were used in the file name. This version fix-es the problem by using UTF-8 for the internal encoding, even if the operating system specifies a different encoding.
- **Improvement:** The workaround introduced in 6.0.5 resulted in a log entry at WARN level on "IBM Java" (AIX, Linux), which is now at (the more appropriate) INFO level, since IBM Java is not affected.
- **Fix:** When retrieving the monitoring page of the sedex client version 6.0.4 via http (http://[host]:[port]/monitoring), the web server may be blocking in certain cases due to race conditions. This version fixes the problem.
- **Workaround:** The JVM Java 8 Update 291 and 292 from April 2021 (other versions are not affected) contain a known issue that causes the sedex client to fail to renew the participant certificate. This can lead to the error message "NoSuchAlgorithmExcep-tion: unrecognized algorithm name" in sedex-controller-technical.log. The sedex client version 6.0.5 contains a specific enhancement which 
works around this JVM error. Nevertheless, we recommend switching to a newer JVM free of this issue as soon as it will become available. <br/> (This change is not relevant for the container version as it is based on a different JVM version.)
- **Update:** The certificate with which remote update packages for the sedex client are digitally signed has been renewed. The corresponding truststore has been extended by this new certificate.
- **Update:** QuoVadis has revoked one of its intermediate certificates due to policy changes and replaced it with a new one. The corresponding truststore has been ad-justed. Note: Older sedex clients automatically download the latest truststore from the sedex server.



#### New Container logic `0.99-beta`
- **New Feature:** Configuration parameters for the sedex client can now also be passed to the sedex client as environment parameters (ENV parameters) of the container. As before, the configuration parameters can be set in the central client configuration file sedex-data/conf/sedex-client-configuration.properties. But now they can also be set as ENV parameters. The ENV parameter has the higher priority, i.e. if a value is defined in both places, the value of the ENV parameter is used.
- **Fix:** When using self-created user and group for the container, some scripts in the container could not be executed correctly under certain circumstances, which could lead to a non-functioning sedex client. This problem is fixed in this version.



### Version/Tag 6.0.4_container-0.99-beta (2021-04-07)

#### New sedex Client `6.0.4`

This release does not introduce any new functionality, but improves some operational and security aspects of the sedex client and its installation program.

- **Improvement: Changes to WS-Proxy user permissions are applied automatically without a restart.** Since version 6.0.x, the sedex client offers the feature of assigning a user name and password to business applications in the configuration file <sedex_home>/conf/sedex-wsproxy-user-configuration.properties. This allows partici-pants themselves to control which web services offered by the sedex 
WS-Proxy their business applications have access to. In previous versions of sedex Client (6.0.2 and 6.0.3), it was necessary to restart the WS-Proxy after changing this configuration file in order to apply the new permissions. As of sedex Client version 6.0.4, a restart is no longer required after changes to the sedex-wsproxy-user-configuration.properties file. WS-Proxy watches this file and detects changes during normal operation and applies them automatically. Whether the changes could be applied correctly can be seen from the technical logfile of the WS-Proxy and is in part also shown on the monitoring page of the WS-Proxy.
- **Change: New default RMI port.** The three processes controller, adapter, WSProxy of the sedex client communicate with each other via an RMI server. The sedex clients from version 6.0.4 cannot use an RMI server of an older client and therefore need a separate RMI server. The new RMI server now uses port 11899 by default. (Older sedex clients 5.x use port 11099 and sedex clients 6.0.2/6.0.3 use 
port 11999 by de-fault). Of course, the RMI port to be used can be reconfigured in the sedex-client-configuration.properties file if necessary. Please consult the sedex client manual for details.
 <br/> (This change is not relevant for the container version as this communication is encapsulated in the container.)
- **Change: Optional Restriction of access rights on new Windows installations.** On the Windows operating system, the installer program now can adjust the default per-missions on the folders of the sedex client. It can limit access to the installing user, the built-in group Administrators and the built-in user SYSTEM. If chosen, all other users and groups will per default have no access to the
 sedex installation folder. After the in-stallation/migration the folder permission have to be adjusted manually by the adminis-trator so that other authorized users and end-user applications have access to the re-quired folders. Please consult the sedex client manual for details.
 <br/> (This change is not relevant for the container version as it is Linux based.)
- **Improvement: Minor internal client improvements.** Several dependencies/libraries have been updated and a few minor internal improvements to the client have been made. These internal improvements have no impact on the functionality, but will make it easier to run in a Docker container, for example.


### Previous Versions

Older versions of the sedex container images had internal test character and are therefore not described in detail here.

If you are interested in the version history of the sedex client and the corresponding changes, you can find these in the separate document *Release Notes* in the download area of [www.sedex.ch](http://www.sedex.ch).
