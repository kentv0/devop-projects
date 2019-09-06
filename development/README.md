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
        ```
        $ export PATH=$PATH:/opt/gradle/gradle-4.0.1/bin
        ```
        For tcsh or csh:
        ```
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
1. Project Directory Structure
    * Create the subdirectories
        ```tcsh
        $ sudo mkdir -p projects/src/main/java/com/kentvo/myapp/customer
        ```
2. Build Configuration Script
    * Change to project's root directory
        ```
        $ cd projects
        ```
    * Create the "build.gradle" file there
        ```groovy
        buildscript {
            repositories {
                mavenLocal()
                maven {
                    url mavenRepoUrl
                    credentials {
                        username System.getenv("MAVEN_USERNAME")
                        password System.getenv("MAVEN_PASSWORD")
                    }
                }
            }
            dependencies {
                classpath "io.spring.gradle:dependency-management-plugin:${versionSpringDependencyPlugin}"
                classpath("org.springframework.boot:spring-boot-gradle-plugin:${versionSpringBootPlugin}")
            }
        }

        apply plugin: 'java'
        apply plugin: 'maven'
        apply plugin: 'io.spring.dependency-management'
        apply plugin: 'org.springframework.boot'
        apply plugin: 'eclipse'

        repositories {
            mavenLocal()
            maven {
                credentials {
                    username System.getenv("MAVEN_USERNAME") ?: System.getProperty("MAVEN_USERNAME")
                    password System.getenv("MAVEN_PASSWORD") ?: System.getProperty("MAVEN_PASSWORD")
                }
                url mavenRepoUrl
            }
        }

        sourceCompatibility = 1.8
        targetCompatibility = 1.8

        task wrapper(type: Wrapper) {
            gradleVersion = versionGradleWrapper
        }

        dependencyManagement {
            imports {
                mavenBom "io.spring.platform:platform-bom:${versionSpringPlatform}"
                mavenBom "org.springframework.cloud:spring-cloud-dependencies:${versionSpringCloud}"
            }
            resolutionStrategy { cacheChangingModulesFor 0, 'seconds' }
        }

        dependencies {
            compile "org.springframework.boot:spring-boot-starter-web"
            compile "org.springframework.boot:spring-boot-starter-actuator"

            compile "org.springframework.cloud:spring-cloud-stream"
            compile "org.springframework.cloud:spring-cloud-starter-config"
            compile "org.springframework.cloud:spring-cloud-starter-bus-kafka"
            compile "org.springframework.cloud:spring-cloud-starter-stream-kafka"

            testCompile "org.springframework.kafka:spring-kafka-test"
            testCompile "org.springframework.boot:spring-boot-starter-test"
            testCompile "org.springframework.cloud:spring-cloud-stream-test-support"

            testCompile group: "junit",             name: "junit"
            testCompile group: "com.google.inject", name: "guice"

            compile group: 'io.springfox', name: 'springfox-swagger2', version: version_swagger
            compile group: 'io.springfox', name: 'springfox-swagger-ui', version: version_swagger_ui
        }

        test {
            include 'com/kentvo/**/*Test.class'
            include 'com/kentvo/**/*IntTest.class'
        }

        jar {
            version  = project.version
            baseName = project.artifact
        }

        task copyJar(type: Copy) {
            from jar
            into 'build/cf/'
            rename '(.*).jar', 'app.jar'
        }

        build.dependsOn copyJar

        install {
            repositories.mavenInstaller {
                pom.groupId    = project.group
                pom.version    = project.version
            }
        }

        configurations.all { resolutionStrategy.cacheChangingModulesFor(0, 'seconds'); }

        mavenRepoUrl = project.version.contains("-SNAPSHOT")? mavenRepoSnapshotsUrl : mavenRepoReleasesUrl

        uploadArchives {
            repositories {
                mavenDeployer {
                    repository(url: mavenRepoUrl) {
                        pom.groupId    = project.group
                        pom.version    = project.version
                        pom.artifactId = project.artifact
                        authentication(userName: System.getenv("MAVEN_USERNAME"),
                                         password: System.getenv("MAVEN_PASSWORD"))
                    }
                }
            }
        }
        ```
3. Gradle Properties
    * Define properties in the ```gradle.properties``` file
        ```groovy
        group    = com.kentvo.myapp.customer
        artifact = myapp-customer
        version  = 1.0.0-SNAPSHOT
        release  = 1

        versionSpringCloud            = Finchley.M9
        versionSpringPlatform         = Cairo-RELEASE
        versionSpringBootPlugin       = 2.0.1.RELEASE
        versionSpringDependencyPlugin = 1.0.3.RELEASE
        version_swagger               = 2.6.1
        version_swagger_ui            = 2.5.0
        versionGradleWrapper          = 4.0.1

        mavenRepoUrl          = http://localhost:8081/repository/kentvo-central
        mavenRepoReleasesUrl  = http://localhost:8081/repository/kentvo-releases
        mavenRepoSnapshotsUrl = http://localhost:8081/repository/kentvo-snapshots
        ```
4. Wrapper Script
    * Open a ```Terminal```
    * Change into the project's root directory
        ```tcsh
        $ cd projects/
        ```
    * Generate wrapper files with the command:
        ```tcsh
        $ gradle wrapper --gradle-version=4.0.1 --distribution-type=bin
        ```

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
