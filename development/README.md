Development
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
Installing Gradle Build Tool
------
1. Download
    * Open a ```Terminal```
    * Download Gradle version ```4.0.1``` with the command:
        ```tcsh
        $ curl -L -O https://services.gradle.org/distributions/gradle-4.0.1-bin.zip
        ```
2. Extract
    * Create directory
        ```tcsh
        $ sudo mkdir -p /opt/gradle
        ```
    * Unzip the downloaded file
        ```tcsh
        $ sudo unzip -d /opt/gradle gradle-4.0.1-bin.zip
        ```
3. Configure
    * Set PATH environment variable

        For bash:
        ```bash
        $ export PATH=$PATH:/opt/gradle/gradle-4.0.1/bin
        ```
        For tcsh or csh:
        ```tcsh
        $ setenv PATH $PATH":/opt/gradle/gradle-4.0.1/bin"
        ```
4. Verify
    * Show version
        ```tcsh
        $ gradle -v
        ```
    * Expected output:
        ```tcsh
        ------------------------------------------------------------
        Gradle 4.0.1
        ------------------------------------------------------------

        Build time:   2017-07-07 14:02:41 UTC
        Revision:     38e5dc0f772daecca1d2681885d3d85414eb6826

        Groovy:       2.4.11
        Ant:           Apache Ant(TM) version 1.9.6 compiled on June 29 2015
        JVM:          1.8.0_201 (Oracle Corporation 25.201-b09)
        OS:            Mac OS X 10.13.6 x86_64
        ```

Setting Up Build Environment
------
1. Directory Structure

    *
    *
2. Build Configuration Script

    *
    *
3. Gradle Properties

    *
    *
4. Wrapper Script

    *
    *

Setting Up Spring Boot
------
1. Resource Representation Class

    *
    *
2. Resource Controller Class

    *
    *
3. Application Class

    *
    *
4. Executable JAR

    *
    *
5. Test

    *
    *
