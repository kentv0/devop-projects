Infrastructure
======
Introduction
------
Prerequisites
------
* CPU: 4
* RAM: 4GB minimum
* OS: Any (this guide is using MacOS)
* Java (JDK or JRE version 8 or higher)

Installing Java
------
1. Download
    * Grab the compatible Java version from this link [here](http://www.oracle.com/technetwork/java/javase/downloads "Java Download")
2. Install
    * Run the downloaded file
    * Follow the instructions to complete the installation
3. Verify
    * Open a ```Terminal```
    * Show Java version with the command:
        ```tcsh
        $ java -version
        ```
    * Expected output should be:
        ```tcsh
        java version "1.8.0_201"
        ```
Installing Docker Desktop
------
1. Download
    * Grab the stable Docker Desktop from this link [here](https://download.docker.com/mac/stable/Docker.dmg "Docker Download") (select MacOS or Windows)
2. Install
    * Run the downloaded file
    * Follow the instructions to complete the installation
3. Verify Installation
    * Open a ```Terminal```
    * Show Docker version with the command:
        ```tcsh
        $ docker version
        ```
    * Expected output should be:
        ```tcsh
        Client: Docker Engine - Community
         Version:           19.03.1
         API version:       1.40
         Go version:        go1.12.5
         Git commit:        74b1e89
         Built:             Fri Aug 13 21:18:17 2017
         OS/Arch:           darwin/amd64
         Experimental:      false
        ```
4. Run
    * Launch Docker Desktop
        * On MacOS, hold down the ```Command Key``` then press the ```Spacebar Key``` and search "docker"
        * On Windows, press the ```Windows Key``` and search "docker"
5. Verify Running
    * Open a ```Terminal```
    * List Docker containers with the command:
        ```tcsh
        $ docker ps
        ```
    * Expected output should be:
        ```tcsh
        CONTAINER ID    IMAGE    COMMAND    CREATED    STATUS    PORTS    NAMES
        ```
6. Debug
    * For the following error:
        ```tcsh
        Error response from daemon: Bad response from Docker engine
        ```
    * Wait a few minutes before attempting to verify again
    
Installing GitLab
------
1. Install
    * 
    *
    
Installing Nexus Repository Manager
------
1. Install
    * Open a ```Terminal```
    * Run Docker image of Nexus with the command:
        ```tcsh
        $ docker run -d -p 8081:8081 --name nexus --restart always sonatype/nexus3
        ```
    * Verify
        ```tcsh
        $ docker ps
        ```
    * Expected output should be:
        ```tcsh
        CONTAINER ID    IMAGE               COMMAND                     CREATED         STATUS          PORTS                       NAMES
        1234abcd        sonatype/nexus3     "sh -c ${SONATYPE_DI..."    4 weeks ago     Up 1 second     0.0.0.0:8081->8081/tcp      nexus
        ```
    * Copy container ID for Nexus (required for next step)
2. Admin Password
    * SSH into the Nexus container with the ID and the command:
        ```tcsh
        $ docker exec -it 1234abcd /bin/bash
        ```
    * Show the admin password with the command:
        ```
        $ cat nexus-data/admin.password
        (copy then type "exit" to exit out of container)
        ```
    * Browse to http://localhost:8081 (it might take a few minutes to finish setting up)
    * Expected page should be:
    
        ![alt text](https://raw.githubusercontent.com/kentv0/devops-project/master/infrastructure/nexus_welcome_page.jpg "Nexus Welcome Page")
3. Admin Login
    * Click ```Sign in``` from the upper right-hand corner
        Username: admin
        Password: (from step 2)
3. Configure
    *
    *
    
Installing Kafka Message Brokers
------
Installing ElasticSearch Search Engine
------
