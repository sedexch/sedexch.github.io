# Frequently Asked Questions (FAQ)

**On this page you will find answers to frequently asked questions. Of course, you may also ask your question [directly to us](contact.md).**

!!! tip

    To quickly get to a specific question, you can use the table of contents in the right margin.

## General Questions about sedex

### Can I use sedex for data transfer in my business case?
    
[sedex](sedex-system/overview_sedex.md) is the platform of the Swiss Confederation for secure, traceable and reliable data transport between business applications in the area of e-Government at the federal, cantonal and municipal levels. If there is a regulatory basis, third-party applications can also be integrated via sedex. If you are interested, please [contact sedex](contact.md).


## General Questions about the sedex Client Container Image

### Should I use the container version already now?
    
For more than 13 years, the sedex client has been available as a classic installation for multiple operating systems. This classic operating mode has proven itself and there is a lot of experience with it. If you can run applications classically on hardware servers or in virtual machines, we still recommend this variant for the time being.
    
For a few months now, we have been offering a parallel pre-release version of the sedex client as a Docker image for operation as a container. At the moment, we are deliberately calling this version EXPERIMENTAL to make it clear that this version is not yet intended for the general public and that changes may still be made to the image. However, if you have the necessary infrastructure and know-how for running containers, you can already run the sedex client as a Docker container in consultation with sedex.
    
We expect to release the sedex client Docker image as official version "1.0" in 2021. 


### Why is the sedex client image currently still pre-release?
    
There are already several productive sedex clients running successfully on the basis of the sedex client image. Since sedex use cases are usually business-critical and have to work stably, we want to run a pilot phase with a few containers first to gain experience in a manageable size and closely accompanied by sedex. As soon as we can be sure that there are no problems in the 
image, we will make it generally available as a "1.0" version.

We expect to release the sedex client Docker image as official version "1.0" in 2021.

