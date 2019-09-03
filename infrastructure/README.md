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
        ```
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
        CONTAINER ID        IMAGE               COMMAND                     CREATED         STATUS          PORTS                       NAMES
        <CONTAINER_ID>      sonatype/nexus3     "sh -c ${SONATYPE_DI..."    4 weeks ago     Up 1 second     0.0.0.0:8081->8081/tcp      nexus
        ```
    * Copy container ID for Nexus (required for next step)
2. Admin Password
    * SSH into the Nexus container with the ID and the command:
        ```
        $ docker exec -it <CONTAINER_ID> /bin/bash
        ```
    * Show the admin password with the command:
        ```
        $ cat nexus-data/admin.password
        ```
        Copy then type "exit" to exit out of container
3. Admin Login
    * Browse to http://localhost:8081

      It might take a few minutes to finish setting up
    * Expected page should be:
    
        ![alt text](https://raw.githubusercontent.com/kentv0/devops-project/master/infrastructure/nexus_welcome.jpg "Login")
    * Click ```Sign in``` from the top of the page
        ```
        Username: admin
        Password: (from step 2)
        ```
3. Configure
    * Click ```Server administration and configuration``` cog icon from the top of the page
    * Click ```Repositories``` from the leftside panel under ```Repository```
    
        ![alt text](https://raw.githubusercontent.com/kentv0/devops-project/master/infrastructure/nexus_cog_icon.jpg "Server administration and configuration")
    * Click ```Create repository``` for 9 of following repositories:
    
        ```
        Recipe: maven2 (proxy)
        Name: spring-io-milestone
        Layout policy: Strict
        Remote storage: https://repo.spring.io/milestone
        ```
        ```
        Recipe: maven2 (proxy)
        Name: maven-geotools
        Layout policy: Permissive
        Remote storage: http://download.osgeo.org/webdav/geotools/
        ```
        ```
        Recipe: maven2 (proxy)
        Name: maven-reslet
        Layout policy: Permissive
        Remote storage: https://maven.reslet.com
        ```
        ```
        Recipe: npm (proxy)
        Name: somename-npm
        Remote storage: https://registry.npmjs.org/
        ```
        ```
        Recipe: npm (group)
        Name: somename-npm-group
        Member repositories: somename-npm
        ```
        ```
        Recipe: maven2 (hosted)
        Name: somename-3rdparty
        Layout policy: Strict
        ```
        ```
        Recipe: maven2 (hosted)
        Name: somename-releases
        Layout policy: Strict
        ```
        ```
        Recipe: maven2 (hosted)
        Name: somename-snapshots
        Layout policy: Strict
        ```
        ```
        Recipe: maven2 (group)
        Name: somename-central
        Member repositories (group in order):
        maven-public, maven-snapshots, maven-releases, maven-central, somename-snapshots,
        somename-releases, somename-3rdparty, maven-geotools, spring-io-milestone, maven-reslet
        ```
Installing Kafka Message Brokers
------
1. Grab IP address
    * On MacOS, open a ```Terminal``` and enter the command:

        ```tcsh
        $ ipconfig getifaddr en0
        ```
        Try "en1" instead of "en0" if nothing returns
    * On Windows, open a ```Command Prompt``` and enter the command:
    
        ```
        C:\Users\Name> ipconfig
        ```
        For wired, "IPv4 Address" under "Ethernet adapter Ethernet"
        
        For wireless, "IPv4 Address" under "Wireless LAN adapter Wi-Fi"
        
2. Create the ```docker-compose.yml``` file with the IP address

    ```yaml
    version: '2'
    services:
      zookeeper:
        image: wurstmeister/zookeeper
        ports:
          - "2181:2181"
      kafka:
        image: wurstmeister/kafka
        ports:
          - "9092:9092"
        environment:
          KAFKA_ADVERTISED_HOST_NAME: <IP_Address>
          KAFKA_CREATE_TOPICS: "test:1:1"
          KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
        volumes:
          - /var/run/docker.sock:/var/run/docker.sock
    ```
3. Start
    Must be in same directory where the ```docker-compose.yml``` file was created
    ```tcsh
    $ docker-compose up -d
    ```
    The "-d" option detaches the container and starts it in the background
4. Test
    * Copy container ID
        ```tcsh
        $ docker ps
        CONTAINER ID    IMAGE               COMMAND             CREATED         STATUS          PORTS                   NAMES
        <CONTAINER_ID>  wurstmeister/kafka  "start-kafka.sh"    3 weeks ago     Up 51 minutes   0.0.0.0:9092->9092/tcp  projects_kafka_1
        ...
        ```
    * Create a topic
        ```
        $ docker exec -it <CONTAINER_ID> kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partition 1 --topic test
        ```
        * Verify created topic
        ```
        $ docker exec -it <CONTAINER_ID> kafka-topics.sh --list --bootstrap-server localhost:9092
        ```
        * Topic should be included in the expected output:
        ```
        __consumer_offsets
        test
        ```
    * Send messages
        ```
        $ docker exec -it <CONTAINER_ID> kafka-console-producer.sh --broker-list localhost:9092 --topic test
        > This is a message
        > This is another message
        ```
        Press ```Control``` + ```c``` to exit
    * Consume messages
        ```
        $ docker exec -it <CONTAINER_ID> kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
        This is a message
        This is another message
        Processed a total of 2 messages
        ```
        Press ```Control``` + ```c``` to exit
    * Delete a topic
        ```
        $ docker exec -it <CONTAINER_ID> kafka-topics.sh --delete --bootstrap-server localhost:9092 --topic test
        ```
        * Verify deleted topic
        ```
        $ docker exec -it <CONTAINER_ID> kafka-topics.sh --list --bootstrap-server localhost:9092
        ```
        * Topic should NOT be included in the expected output:
        ```
        __consumer_offsets
        ```
5. Stop
    * Must explicitly shut down
        ```tcsh
        $ docker-compose down
        ```
    * Expected output:
        ```tcsh
        Stopping projects_zookeeper_1 ... done
        Stopping projects_kafka_1     ... done
        Removing projects_zookeeper_1 ... done
        Removing projects_kafka_1     ... done
        Removing network projects_default
        ```
Installing ElasticSearch Search Engine
------
1. Setup
    * Create a Docker network with the command:
        ```tcsh
        $ docker network create <NETWORK_NAME>
        ```
    * Verify created network
        ```tcsh
        $ docker network ls
        ```
    * Network should be included in the expected output:
        ```
        NETWORK ID      NAME                DRIVER          SCOPE
        1005b83d5ef6    bridge              bridge          local
        b7f8d6aa972f    host                host            local
        bc4094b76759    <NETWORK_NAME>      bridge          local
        ...
2. Start
    * Run Docker image of ElasticSearch
        ```tcsh
        $ docker run -d --name elasticsearch --restart always --net <NETWORK_NAME> -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.6.1
        ```
3. Test
    * Create an index
        ```tcsh
        $ curl -X PUT "http://localhost:9200/<INDEX_NAME>?pretty"
        {
            "acknowledged" : true,
            "shards_acknowledged" : true,
            "index" : "<INDEX_NAME>"
        }
        ```
    * Insert data to index
        ```tcsh
        $ curl -X POST "http://localhost:9200/<INDEX_NAME>?_create"
Installing GitLab
------
