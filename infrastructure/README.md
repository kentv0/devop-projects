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
    * Enter the following command:
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
    * Enter the following command:
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
        * On MacOS, hold down the ```Command``` key then press the ```Spacebar``` key and search "docker"
        * On Windows, press the ```Windows``` key and search "docker"
5. Verify Running
    * Open a ```Terminal```
    * Enter the following command:
        ```tcsh
        $ docker ps
        ```
    * Expected output should be:
        ```tcsh
        CONTAINER ID    IMAGE    COMMAND    CREATED    STATUS    PORTS    NAMES
        ```
Installing GitLab
------
1. Install
    *
    *
    
Installing Nexus Repository Manager
------
1. Install
    * 
    *
    
2. Verify
    *
    *
    
3. Login
    *
    *
    
3. Configure
    *
    *
    
Installing Kafka Message Brokers
------
Installing ElasticSearch Search Engine
------
