# Phase 1: Initial Configuration


**If you are starting from scratch, i.e. you have never started a sedex Client as a Docker container for the participant before, then you must first execute the Phase 1 which is the *Initial Configuration*.**


![Phase 1: Initial Configuration](/assets/v6/phase-1.png)


During this one time configuration process, the container will automatically prepare the *external permanent data storage* by creating the required directory structure and configuration files based on an initial configuration file you have to provide.



## Initial Configuration File ("init.conf")

In order to execute the *initial configuration process*, you have to provide a prepared *initial configuration file* ("init.conf").
The format of this file is plain text and has to contain the *initial configuration parameters*.
The exact parameters to be listed in this "init.conf" file depend on whether you already have a sedex participant certificate or not.



## Identify your Case of Initial Configuration

The content of the initial configuration file "init.conf" depends on the type of credentials you have for the participant. **You will find yourself in one of the following two cases:**

   - **[Case a) Initial configuration for an existing sedex participant:](/client-container-v6/initial-configuration/initial_configuration_with_certificate)**<br />
  Follow the instructions for Case a) if a sedex Client has been running in another installation (Container and non-Container) for your participant before. Thus a sedex *participant-certificate* (the P12 file) and a corresponding *password* already exist and must be reused.
  

   - ** [Case b) Initial configuration for a new sedex participant:](/client-container-v6/initial-configuration/initial_configuration_with_crid_and_otp)
**<br />
  Follow the instructions for Case b) if no sedex Client has been running for your participant before. Thus there is *no* participant-certificate yet but a so-called *certificate request id (CRID)* and a corresponding *one time password (OTP)*.


 


