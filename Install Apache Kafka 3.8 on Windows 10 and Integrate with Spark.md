# Install Apache Kafka 3.8 on Windows 10 and Integrate with Spark
**Apache Kafka** is an open-source distributed streaming processing platform used to handle the real time data storage with a processing capacity of millions of data or messages per second. Apache Kafka 
was originally developed at **LinkedIn** and later donated to **Apache Software Foundation** and is currently maintained by **Confluent** under Apache Software Foundation.

Apache Kafka works as a **broker** between sender and receiver and provides a **messaging system** (responsible for exchanging messages between two applications) in publish-subscribe (pub-sub) model. 
In pub-sub model, a sender _(known as **Producer** or Publisher)_ sends/writes a message (nothing but data) to a persisted location called **topic** and then receiver _(known as **Consumer** or Subscriber)_ 
consumes a message by subscribing to a topic.

The key components of Apache Kafka include **Topic**, **Broker**, **Producer**, **Consumer** and **Zookeeper** and provides the following core APIs:
1. **Producer API:** It allows an application to publish streams of records to one or more topics.
2. **Consumer API:** It allows an application to subscribe to one or more topics and process the stream of records produced to them.
3. **Streams API:** It allows an application to effectively transform streams of records from input topics to output topics i.e. an application acts as a stream processor to consume an input stream
   from one or more topics, and produce an output stream to one or more output topics.
4. **Connector API:** It allows implementing connectors to move large amounts of data from source system to Kafka or push from Kafka into some sink data system.
5. **Admin API:** It allows us to manage and inspect topics, partitions, brokers, ACLs and other Kafka objects.

Apache Kafka is heavily used across many organizations such as **Netflix**, **UBER**, **Walmart**, **Twitter**, **LinkedIn**, **Mozilla**, **Oracle** etc. with several use cases such as:
* **Messaging:** Kafka works better as a replacement for traditional message broker since it provides publish-subscribe messaging system.
* **Metrics:** Kafka is used for operational monitoring data by aggregating statistics from distributed applications.
* **Even Sourcing:** Kafka is an excellent backend for applications of event sourcing since it supports very large stored data.
* **Log Aggregation:** Kafka can be used across an organization to collect logs from multiple services and make them available in a standard format to multiple consumers.
* **Stream Processing:** Kafka’s strong durability is useful for stream processing in case of some popular frameworks such as Storm and Spark Streaming to read data from a topic, process it and write processed 
data to a new topic which becomes available for users and applications.
* **Website Activity Tracking:** Kafka can be used as a user activity tracking pipeline of a website as a set of real-time publish-subscribe feeds where each site activity that includes page views, searches, 
etc. is published to respective central topics. 

This document provides instructions to install Apache Kafka with two types of configuration – **Single broker** and **Multiple brokers** and execute a sample Kafka project to produce data to and consume data 
from Kafka cluster. We will also see how to integrate Kafka cluster with Spark to read data and process it.  
You can also go through [this PDF document](/doc/Install%20Apache%20Kafka%203.8%20on%20Windows%2010.pdf) for installation steps along with screenshots.  
<br/>

## 1. Prerequisites:
The following prerequisites need to be installed before running Kafka.
1. **File Archiver:** Any file archiver such as **7zip** or **WinRAR** is needed to unzip the downloaded Spark binaries.
   **7zip** can be downloaded from the [official 7zip Downloads](https://www.7-zip.org/download.html) website where as **WinRAR** can be downloaded from the [official RAR lab Downloads](https://www.rarlab.com/download.htm) website.

2. **JRE 8:** Kafka 3.x requires Java 8 runtime environment.
   We can either download just **JRE 8** (Java Runtime Environment) for Windows offline installation from the official [Java Download for Windows Offline](https://www.java.com/en/download/windows_offline.jsp) 
   website or download the whole **JDK 8** (Java Development Kit) directly from [Oracle Java Downloads](https://www.oracle.com/java/technologies/downloads/#java8) website.
   For the complete JDK installation steps, look at [here](https://github.com/srimarrivada/JDK-Installation/blob/main/Install%20JDK8%20on%20Windows.md).
<br/>

## 2. Install Kafka:
Let us see the step by step procedure to install Apache Kafka in Windows.

### 2.1. Download Kafka Binaries:
* Go to [Apache Kafka Downloads](https://kafka.apache.org/downloads) website. 
* Select the latest release _(at the time of this document preparation, the most recent release is 3.8.0)_ and you will find the multiple Kafka build versions of Scala where you can select either **Scala 2.12**
  or **Scala 2.13** version for the Scala version that you use. Let us choose **Scala 2.13 version of Kafka 3.8.0** release.
* Click on `kafka_2.13-3.8.0.tgz` link which downloads the file into your **Downloads** folder.
* After the binary file is downloaded, choose the installation directory in your machine and copy `kafka_2.13-3.8.0.tgz` file to that directory. Let us choose the installation directory as
  `D:\ProgramFiles\Kafka`.
* Right click on `kafka_2.13-3.8.0.tgz` file and choose **7-Zip** -> **Extract Here** option which extracts a new packed file `kafka_2.13-3.8.0.tar`.
* Next, unpack `kafka_2.13-3.8.0.tar` file using **7zip** utility.
* The tar file extraction may take few minutes to finish. After finishing, you see a folder named `kafka_2.13-3.8.0` which consists of Kafka binaries and libraries.

_**<ins>Note:</ins>**_  
When your Kafka Installation path is too long, you might encounter an error **"The input line is too long"** while executing Kafka scrips. To avoid this error, move all folders and files 
from `D:\ProgramFiles\Kafka\kafka_2.13-3.8.0` location to `D:\ProgramFiles\Kafka` folder.

### 2.2. Set up Environment variables:
After installing pre-requisites and Kafka binaries, we should configure the below environment variables defining Java, and Kafka default paths.

* `JAVA_HOME`: This is the JDK installation directory path in the machine _(in my machine, it is `D:\ProgramFiles\Java\jdk-1.8`)_. Ignore if this is already done.
* `KAFKA_HOME`: This is the Kafka installation directory path in the machine _(in our case, it is `D:\ProgramFiles\Kafka`)_

_**Note:**_  
These variables need to be added to either **User environment** variables or **System environment variables** depending on Kafka configuration needed **for a single user** or **for multiple users**.  
  
In this tutorial, we will add **User environment variables** since we are configuring Kafka for a single user but if you would like to configure Kafka for multiple users, then define System environment variables.  
<br/>

Follow these steps to set environment variables:
1. In the Windows search bar, start typing “environment variables” and select the first match which opens up **System Properties** dialog.

2. On the **System Properties** window, press **Environment Variables** button.

3. On the **Environment Variables** dialog:
   * Press New under **User variables** section. Add variable name `JAVA_HOME` and value `D:\ProgramFiles\Java\jdk-1.8` _(the path where your Java was installed)_. Then, press OK.
   * Press New again. Add variable name `KAFKA_HOME` and value `D:\ProgramFiles\Kafka`_(the path where your Kafka was installed)_ and press OK.
   * Select **Path** variable and press **Edit** button. Press **New** and add the following values and press OK.  
     `%JAVA_HOME%\bin`  
     `%KAFKA_HOME%\bin`  
   * Press OK to apply environment variable changes and close window.

### 2.3. Verify Kafka Installation:
Open either **Command Prompt** or **Windows PowerShell** prompt and run the following command:
```
kafka-topics.bat --version
```
After the above command is executed, you can see the installed version **3.8.0**.
<br/>
<br/>

## 3. Configure Single Node-Single Broker:
In this configuration type, we wil set up a single Zookeeper with single Broker instance running on the single machine.

### 3.1. Configure Kafka Logs:
By default `zookeeper.properties` and `server.properties` files are configured with default directory locations that should be updated to the desired locations.

Go to `%KAFKA_HOME%\config` location:
* Open `zookeeper.properties` file and change the `dataDir` value from `/tmp/zookeeper` to `D:/ProgramFiles/Kafka/zookeeper`.
* Open `server.properties` file and scroll down to `log.dirs` and change the value from `/tmp/kafka-logs` to `D:/ProgramFiles/Kafka/kafka-logs`
 
Note that `kafka-logs` and `zookeeper` directories will be created automatically in `%KAFKA_HOME%` location when Zookeeper and Kafka broker services are started.

### 3.2. Start Kafka Servers:
Now, we will start the Zookeeper and Kafka Broker services in the local system.

#### Start Zookeeper:
The Zookeeper is a centralized service in Apache Kafka that manages configuration changes and provides distributed configuration. It is responsible for managing the Kafka cluster by performing broker leader 
election and provides metadata to brokers about the processes running in the system and facilitate health checking of each broker in the cluster. 

Open **Command Prompt** as **Administrator** and start Zookeeper using the below command:
```
zookeeper-server-start.bat %KAFKA_HOME%\config\zookeeper.properties
```

On the console, you will see a message **"binding to port 0.0.0.0/0.0.0.0:2181"** which indicates that the Zookeeper is up and running. Do not close this command prompt window.

#### Start Kafka Broker:
The Kafka broker manages the storage of messages in topics. Kafka cluster typically consists of multiple brokers to maintain the load balance and Kafka broker leader election is done by Zookeeper.

Open **Command Prompt** as **Administrator** and start Kafka broker using the below command:
```
kafka-server-start.bat %KAFKA_HOME%\config\server.properties
```
On the console, you will see a message **"Registered broker 0 at path /brokers/ids/0"** which indicates that the Kafka Broker is up and running. Do not close this command prompt window.  

Run this command to verify all Kafka services have been started:
```
jps
```
It should display two demons **QuroumPeerMain** and **Kafka** process which denotes Zookeeper, and single Kafka Broker services are running.

### 3.3. Topics:
Kafka provides a command line utility `kafa-topics.bat` to manage topics _(create, list, describe, delete, etc)_ on the cluster. This utility requires `-bootstrap-server` argument which takes Kafka broker 
instance details in `hostname:port` format.

#### Create Topic:
By default, a topic is created with 1 replication factor and 1 partition unless explicitly specified. In Kafka, we cannot specify more than 1 replication factor when a single broker is running in the 
Kafka cluster _(i.e. maximum replication factor value = number of Kafka brokers)_.

Open **Windows PowerShell** or **Command Prompt** and run the below command to create a Kafka topic:
```
kafka-topics.bat --bootstrap-server localhost:9092 --create --topic test-topic
```
On the Kafka broker terminal window, you can see a message for the create topic `test-topic` and it's log location.

The below command creates another Kafka topic with 4 partitions:
```
kafka-topics.bat --bootstrap-server localhost:9092 --create --topic test-topic-part4 --replication-factor 1 --partitions 4
```
On the Kafka broker terminal window, you can see a message for the created topic `test-topic-part4` and it's log location for each partition.

#### List Topics:
Use this command to list out all available topics in Kafka server:
```
kafka-topics.bat --bootstrap-server localhost:9092 --list
```

#### Describe Topics:
Use this command to get more details including topic partitions and replication information of all topics in Kafka server:
```
kafka-topics.bat --bootstrap-server localhost:9092 --describe
```

### 3.4. Kafka Producer:
Kafka distribution provides a command line utility named `kafa-console-producer.bat` to publish messages into a Kafka topic.

Open **Command Prompt** or **Windows PowerShell** and run the following command to start producing messages:
```
kafka-console-producer.bat --bootstrap-server localhost:9092 --topic Hello-Kafka
```

When the above command is executed, it creates a topic `Hello-Kafka` and waits for input from user to publish topic. You can enter few lines of messages on the producer terminal. 

By default, every new line entered on the producer console is published as a new message to Kafka and this configuration can be changed in `%KAFKA_HOME%\config\producer.properties` file.

### 3.5. Kafka Consumer:
Kafka distribution provides a command line utility named `kafa-console-consumer.bat` to subscribe messages from a Kafka topic.

Open **Command Prompt** or **Windows PowerShell** and run the following command to start consuming messages:
```
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic Hello-Kafka --from-beginning
```

Here, you will see all messages that were produced to `Hello-Kafka` topic until now and will keep getting the new messages that will be produced to this topic. 

Now, go to the producer window and enter a new message and then go to the consumer window where you can see the latest message that was produced

Use **Ctrl + C** to exit out of Producer and Consumer windows.

### 3.6. Delete Topic:
Use the following command to delete the topic named `test-topic` in Kafka cluster:
```
kafka-topics.bat --bootstrap-server localhost:9092 --delete --topic test-topic
```
**Note:**  
After issuing `--delete --topic` command for Kafka running in Windows, it will only mark the respective topic name for deletion but will not physically delete the topic folder present in 
`%KAFKA_HOME%\kafka-logs` folder.

On the Kafka broker terminal window, you will see an **Error while renaming dir for test-topic-0 in log dir D:\ProgramFiles\Kafka\kafka-logs** and then Kafka server was shut down.
This issue occurs in Windows operating system since Kafka is intended to run on Linux operating system. If we try to run Kafka in Windows natively, it may arise several issues over a period of time. 

To resolve the above error, follow the below steps to manually delete the respective topic folder:
* Open new **Command Prompt** as **Administrator** and stop the zookeeper with this command:
```
zookeeper-server-stop.bat %KAFKA_HOME%\config\zookeeper.properties
```

* Go to `%KAFKA_HOME%` location and delete `kafka-logs` and `zookeeper` folders. 

* Open new **Command Prompt** as **Administrator** and start zookeeper with this command: 
  ```
  zookeeper-server-start.bat %KAFKA_HOME%\config\zookeeper.properties
  ```

* Open new **Command Prompt** as **Administrator** and start Kafka broker with this command: 
  ```
  kafka-server-start.bat %KAFKA_HOME%\config\server.properties
  ```

* Go to `%KAFKA_HOME%` location and you can see `kafka-logs` and `zookeeper` folders were created freshly.

Since all topics were deleted, we need to start creating a topic freshly.
<br/>
<br/>

## 4.	Configure Single Node-Multiple Brokers:
In this configuration type, we will set up a single Zookeeper with multiple Broker instances running on the single machine.

#### 4.1. Setup Multiple Brokers:
In the previous configuration, one Kafka broker instance was already configured in `server.properties` file. 
To setup multiple broker instances, take two copies of `server.properties` file and rename as `server-one.properties` and `server-two.properties` in `%KAFKA_HOME%\config` location. 

Open `server-one.properties` and change the following settings:
```
broker.id=1
listeners=PLAINTEXT://:9093
log.dirs=D:/ProgramFiles/Kafka/kafka-logs-1
```

Open `server-two.properties` and change the following settings:
```
broker.id=2
listeners=PLAINTEXT://:9094
log.dirs=D:/ProgramFiles/Kafka/kafka-logs-2
```

Note that `kafka-logs-1` and `kafka-logs-2` folders will be created automatically in `%KAFKA_HOME%` when Kafka brokers are started.

### 4.2. Start Kafka Servers:
Now, we will start the Zookeeper and multiple Kafka Broker instances in the local system.

#### Start Zookeeper:
Open **Command Prompt** as **Administrator** and restart Zookeeper using the below commands:
```
zookeeper-server-stop.bat %KAFKA_HOME%\config\zookeeper.properties

zookeeper-server-start.bat %KAFKA_HOME%\config\zookeeper.properties
```

On the console, you will see a message **"binding to port 0.0.0.0/0.0.0.0:2181"** which indicates that the Zookeeper is up and running. Do not close this command prompt window.

#### Start Kafka Brokers:
Open another **Command Prompt** as **Administrator** and restart the first Kafka broker instance using the below commands:
```
kafka-server-stop.bat %KAFKA_HOME%\config\server.properties

kafka-server-start.bat %KAFKA_HOME%\config\server.properties
```
On the console, you will see a message **"Registered broker 0 at path /brokers/ids/0"** which indicates that the first Kafka Broker is up and running. Do not close this command prompt window.  

Open another **Command Prompt** as **Administrator** and start the second Kafka broker instance using the below command:
```
kafka-server-start.bat %KAFKA_HOME%\config\server-one.properties
```
On the console, you will see a message **"Registered broker 1 at path /brokers/ids/1"** which indicates that the second Kafka Broker is up and running. Do not close this command prompt window.  

Open another **Command Prompt** as **Administrator** and start the third Kafka broker instance using the below command:
```
kafka-server-start.bat %KAFKA_HOME%\config\server-two.properties
```
On the console, you will see a message **"Registered broker 2 at path /brokers/ids/2"** which indicates that the third Kafka Broker is up and running. Do not close this command prompt window.

Run this command to verify the Kafka services:
```
jps
```
It should display one **QuroumPeerMain** daemon and three **Kafka** daemons which denotes one Zookeeper, and three Kafka Broker services are running.

### 4.3. Topics:

#### Create Topic:
Open **Windows PowerShell** or **Command Prompt** and run the below command to create a new Kafka topic with replication factor 3 _(since we have 3 broker instances running)_
```
kafka-topics.bat --bootstrap-server localhost:9092 --create --topic Multibrokerapplication --replication-factor 3 --partitions 1
```

### Describe Topic:
Use the below command to check which broker is running on the current created topic:
```
kafka-topics.bat --bootstrap-server localhost:9092 –describe --topic Multibrokerapplication
```
After executing the above command, you will see:
* One of the brokers _(in my case, Leader 0 with broker.id 0)_ is elected as leader.
* Dispalys **Replicas:0,2,1** indicating that all the brokers replicated the topic.
* Isr is the set of in-sync replicas (a subset of replicas that are currently alive and caught up by the leader).

### 4.4. Kafka Producer:
Open **Command Prompt** or **Windows PowerShell** and run the following command to start producing messages to second broker running on port `9093`:
```
kafka-console-producer.bat --bootstrap-server localhost:9093 --topic Multibrokerapplication
```
It creates a topic named `Multibrokerapplication` and waits for inputting data.

### 4.5. Kafka Consumer:
Since `Multibrokerapplication` topic was created with replication factor 3, we can connect to any broker in the cluster and start consuming messages.  
Open a new **Command Prompt** or **Windows PowerShell** and run any of the following commands to consume messages: 
```
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic Multibrokerapplication --from-beginning
```
```
kafka-console-consumer.bat --bootstrap-server localhost:9093 --topic Multibrokerapplication --from-beginning
```
```
kafka-console-consumer.bat --bootstrap-server localhost:9094 --topic Multibrokerapplication --from-beginning
```
You will see all messages that are produced to `Multibrokerapplication` topic until now and will keep getting the new messages that will be produced to this topic. 

Use **Ctrl + C** to exit out of Producer and Consumer windows.

### 4.6. Delete Topic:
Use the following command to delete the topic named `test-topic` in Kafka cluster:
```
kafka-topics.bat --bootstrap-server localhost:9094 --delete --topic Multibrokerapplication
```
_**Note:**_  
After issuing `--delete --topic` command for Kafka running in Windows, it will only mark the respective topic name for deletion but will not physically delete the topic folder present in 
`kafka-logs`, `kafka-logs-1` and `kafka-logs-2` folders in `%KAFKA_HOME%` location.

On the three Kafka broker terminal windows, you will see an **Error while renaming dir for test-topic-0 in log dir D:\ProgramFiles\Kafka\kafka-logs** and then Kafka server was shut down.

The above issue occurs in Windows operating system since Kafka is intended to run on Linux operating system. If we try to run Kafka in Windows natively, it may arise several issues over a period of time. 

To resolve the above error, follow the below steps to manually delete the respective topic folder:
* Open new **Command Prompt** as **Administrator** and stop the zookeeper with this command:
```
zookeeper-server-stop.bat %KAFKA_HOME%\config\zookeeper.properties
```

* Go to `%KAFKA_HOME%` location and delete `zookeeper`, `kafka-logs`, `kafka-logs-1` and `kafka-logs-2` folders.

* Open new **Command Prompt** as **Administrator** and start zookeeper with this command: 
  ```
  zookeeper-server-start.bat %KAFKA_HOME%\config\zookeeper.properties
  ```

* Open three Command Prompts as **Administrator** and start three Kafka brokers with these commands: 
  ```
  kafka-server-start.bat %KAFKA_HOME%\config\server.properties
  ```
  ```
  kafka-server-start.bat %KAFKA_HOME%\config\server-one.properties
  ```
  ```
  kafka-server-start.bat %KAFKA_HOME%\config\server-two.properties
  ```
  
* Go to `%KAFKA_HOME%` location and you can see `zookeeper`, `kafka-logs`, `kafka-logs-1` and `kafka-logs-2` folders were created freshly.
Since all topics were deleted, we need to start creating a topic freshly.
<br/>

## 5. Simple Kafka Project using Java:
In the previous section, we used Kafka command line tools such as `kafka-topics.bat`, `kafka-console-producer.bat` and `kafka-console-consumer.bat` to create a Kafka topic and start producing and 
subscribing data.  
In this section, we will create a simple Kafka project to do the same activities using Java programming language.

### 5.1. Launch Java IDE:
To create a Kafka project using Java, we need an IDE tool such as **Eclipse**, **VS Code**, **Intellij IDEA**, **Notepad**, etc. and a JDK version 1.8 or higher. 

In this tutorial, we will use **VS Code** to create a java project. If you do not have VS Code installed already, you can download it from [official Microsoft site](https://code.visualstudio.com/download) 
to install and then install [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) extension.

In VS Code application, open a new folder and navigate to D drive to create a folder named `Kafka` and select it in your local system.

On the **Explorer** window, click on **New File** button next to `KAFKA` folder and name it with `.java` extension (for example, `Hello.java`) after which it starts activating Java extension 
and you can see `JAVA PROJECTS` section on the bottom left of the application.

Once the Java extension is ready, enter the below code in `Hello.java` file and click on **Run** link or button to see the output of java program on the Terminal.
```
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello world!");
    }
}
```

Now that our sample Java code is working, we will write java programs to create a Kafka topic, producer and consumer application. 
To do this, expand `JAVA PROJECTS` window on the left bottom window of the VS Code, then expand `KAFKA` project and click on **+** icon next to **Referenced Libraries** and 
select `D:\ProgramFiles\Kafka\libs\kafka-clients-3.8.0.jar` file.

Note that Apache Kafka provides `kafka-clients-xxx.jar` library (located at `%KAFKA_HOME%\libs` folder) which contains `admin` API, `producer` API and `consumer` APIs using which we will 
create a topic, send data and read data programmatically.

Before proceeding further, make sure Kafka services are up and running. If not already running, open 4 Command Prompts in **Administrator** mode and run the following commands to start Zookeeper first and then 3 Kafka brokers:
```
zookeeper-server-start.bat %KAFKA_HOME%\config\zookeeper.properties
```
```
kafka-server-start.bat %KAFKA_HOME%\config\server.properties
```
```
kafka-server-start.bat %KAFKA_HOME%\config\server-one.properties
```
```
kafka-server-start.bat %KAFKA_HOME%\config\server-two.properties
```

### 5.2. Create Topic:
Apache Kafka provides the `admin` API in which we need to start with using `create()` method of `Admin` interface to establish a connection to Kafka cluster and create a topic. 
* First, we should create an `Admin` object by providing a set of key-value pairs as configuration properties to `create()` method. It is required to specify at least `bootstrap.servers` property 
  which takes a list of host/port pairs used to establish an initial connection to the Kafka cluster. For the complete list of admin properties, refer to 
  [Apache Kafka Admin Client Configs Documentation](https://kafka.apache.org/documentation/#adminclientconfigs).

  **Note:** In `Properties` object, we can set the property `bootstrap.servers` directly or by calling `BOOTSTRAP_SERVERS_CONFIG` variable in `AdminClientConfig` class.

* Once the `Admin` object is created, one or more topics can be created using `createTopics()` method which takes a collection of `NewTopic` objects where we can specify topic name, number of partitions 
  and replication factor for a specific topic.  
  If you omit `partitions` and (or) `replicationFactor` arguments in `NewTopic` object then `topicName` is created with default partitions and replication factor values that are mentioned 
  in `%KAFKA_HOME%\config\server.properties` file.

* `createTopics()` method returns `CreateTopicsResult` object of which `values()` method of which `get()` method is needed to get the result of a specific topic created. This `get()` method returns
  `KafkaFuture<Void>` object.

* `KafkaFuture<Void>` object has `get()` method which helps to wait until the topic creation is either completed or failed. This method can throw 2 exceptions - `InterruptedException` and `ExecutionException` -
  in case of any failure. 

In VS Code, create a new file named `CreateTopic.java` and enter the following code:
```
import java.util.Collections;
import java.util.Properties;
import java.util.concurrent.ExecutionException;

import org.apache.kafka.clients.admin.Admin;
import org.apache.kafka.clients.admin.AdminClientConfig;
import org.apache.kafka.clients.admin.CreateTopicsResult;
import org.apache.kafka.clients.admin.NewTopic;
import org.apache.kafka.common.KafkaFuture;

public class CreateTopic {
    public static void main(String[] args) {
        // Set topic name, partitions and replication factor
        String topic = "topic-1";
        int partitions = 4;
        short replicationFactor = 3;

        // Create instance for properties to access admin configs
        Properties properties = new Properties();
        // Assign bootstrap servers config value
        //properties.setProperty("bootstrap.servers", "localhost:9092");
        properties.setProperty(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");

        // Create instance for admin client
        Admin admin = Admin.create(properties);

        try {
            // Create a compacted topic
            CreateTopicsResult createResult = admin.createTopics(Collections.singleton(new NewTopic(topic, partitions, replicationFactor)));
            // Call values() to get result for a specific topic
            KafkaFuture<Void> future = createResult.values().get(topic);
            
            // Call get() to block until the topic creation is complete or has failed. 
            // Incase failed, the ExecutionException displays the underlying cause.
            future.get();
            System.out.println("'"+topic+"' topic has been created successfully");
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```
After the above code is entered, click on **+** button on **Terminal** window and select **Command Prompt** where you need to run the following commands to compile the `CreateTopic.java` file and run it.
```
javac -cp %KAFKA_HOME%\libs\* CreateTopic.java
java -cp %KAFKA_HOME%\libs\*;. CreateTopic
```

You can see that a topic name `topic-1` was created.

#### List Topics:
We can also list out the available topics in the cluster using `listTopics()` method of `Admin` object which returns `ListTopicsResult` object of which `names()` method of which `get()` method can be used to 
retrieve the list of topic names available in Kafka cluster.

In VS Code, enter the following code before `catch()` block in `CreateTopic.java` file. Since `topic-1` is already created, change the topic variable value to `topic-2` to create a new topic and then list out 
both topics:
```
            // List all topics in Kafka cluster
            ListTopicsResult listResult = admin.listTopics();
            System.out.println("List of all topics:");
            System.out.println(listResult.names().get()); 
```

Once the above code is entered, compile `CreateTopic.java` file and run it after which you will see that it created a new topic `topic-2` and listed out both topics.

#### Describe Topics:
Similar to `listTopics()`, we can get the topic details using `describeTopics()` method of `Admin` object which returns `DescribeTopicsResult` object of which `values()` method of which `get()` method is 
needed to get the result of a specific topic created. This `get()` method returns `KafkaFuture<Void>` object of which `allTopicnames()` method of which `get()` method can be used to retrieve the details 
such of topic name,  partitions, leader, replication factor etc.

In VS Code, enter the following code before `catch()` block in `CreateTopic.java` file. Since `topic-2` is already created, change the topic variable value to `topic-3` to create a new topic and provide 
details of each topic: 
```
            // Describe all topics
            DescribeTopicsResult descResult = admin.describeTopics(listResult.names().get());
            System.out.println("Describe all topics:");
            System.out.println(descResult.allTopicNames().get());
```
Once the above code is entered, compile `CreateTopic.java` file and run it after which you will see that it created a new topic `topic-3` and described all topics.

#### Delete Topics:
Similar to `createTopics()`, we can delete one or more topics using `deleteTopics()` method of `Admin` object which returns `DeleteTopicsResult` object which has `topicNameValues()` method of which `get()` 
method returns `KafkaFuture<Void>` object. The `get()` method of `KafkaFuture<Void>` objects ensures to wait until the topic deletion is either completed or failed and throws 2 exceptions - 
`InterruptedException` and `ExecutionException` - in case of any failure. 

The following code deletes 3 topics in the Kafka cluster:
```
            // Delete multiple topics
            ArrayList<String> topics= new ArrayList<String>();
            topics.add("topic-1");
            topics.add("topic-2");
            topics.add("topic-3");
            DeleteTopicsResult delResult = admin.deleteTopics(topics);
            for (String topicName : topics) {
                KafkaFuture<Void> delFuture = delResult.topicNameValues().get(topicName);
                // Call get() to block until the topic deletion is complete or has failed
                delFuture.get();
                System.out.println(topicName + " has been deleted successfully");
            }
```
**Note:**  
After executing the above code, it appears that topics were deleted, but it marks for deletion without deleting the underlying files in `%KAFA_HOME%\kafka-logs` folder and shuts down all Kafka brokers abruptly. 
This is a known issue in Windows operating system.  
To resolve this, we should manually delete all files under `kafka-logs`, `kafka-logs-1`, `kafka-logs-2`, and `zookeeper` folders in `%KAFA_HOME%` location then restart Zookeeper and Kafka brokers services.

### 5.3. Create Producer:
Now that topics are created in Kafka, let us create a Producer application.

Apache Kafka provides the `producer` API in which we need to start with using `KafkaProducer` class which implements `Producer` class to establish a connection to Kafka cluster and produce data. 
* First, we should instantiate the `Producer` class by providing a set of key-value pairs as configuration properties. Some of the required properties are `bootstrap.servers` (list of host/port pairs), 
  `key.serializer`(serializer class for key) and `value.serializer` (serializer class for value).
  
  Other commonly used properties are `client.id` (unique id for the producer application), `producer.type` (sync or async), `retries` (auto-retry on failure), `batch.size` (batch records), 
  `buffer.memory` (memory to buffer records to be sent to server).
  For the complete list of producer properties, refer to [Apache Kafka Producer Configs Documentation](https://kafka.apache.org/documentation.html#producerconfigs). 

**Note:** In the `Properties` object, we can set properties `bootstrap.servers`, `key.serializer` and `value.serializer` directly or by calling `BOOTSTRAP_SERVERS_CONFIG`, `KEY_SERIALIZER_CLASS_CONFIG` and 
`VALUE_SERIALIZER_CLASS_CONFIG` variables in `ProducerConfig` class.

* The `send()` method of `KafkaProducer` class is used to send a producer record to Kafka. This method takes `ProducerRecord` object and `callback` object (this is optional) as arguments.

* The `ProducerRecord` class manages records to be sent to Kafka for which we need to pass `topic` (topic name) and `value` (data) as mandatory arguments to `ProducerRecord` constructor.
  Additionally, we can provide `key`, `partition` and `timestamp` values as well to `ProducerRecord`.
  
* It is important to flush the data sent and close the producer using `flush()` and `close()` methods provided by `producer` object to avoid any resource leaks.

In VS Code, create a new file named Producer.java and enter the following code:
```
import java.util.Properties;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;

public class Producer {
    public static void main(String[] args) {
        if (args.length < 2){
            System.err.println("Invalid arguments passed, you need to pass topic name and message parameters");
            System.exit(1);
        }

        // Read topic name and message passed as input arguments
        String topicName = args[0];
        String message = args[1];

        // Create instance for properties to access admin configs
        Properties properties = new Properties();

        // Assign bootstrap servers, key and value serializer config values
        //properties.put("bootstrap.servers", "localhost:9092");
        //properties.put("key.serializer","org.apache.kafka.common.serialization.Serializer");
        //properties.put("value.serializer","org.apache.kafka.common.serialization.Serializer");
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.Serializer");
        properties.setProperty(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.Serializer");

        // Create instance for Kafka Producer
        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(properties);

        // Create instance for ProducerRecord using topic name and message
        ProducerRecord<String,String> producerRecord = new ProducerRecord<String,String>(topicName, message);

        // Send producer record
        producer.send(producerRecord);
        System.out.println("Data produced successfully");

        // Flush the producer data
        producer.flush();

        // Close the producer object
        producer.close();
    }
}
```
After the above code is entered, click on **+** button on **Terminal** window and select **Command Prompt** where you need to run the following commands to compile the `Producer.java` file and run it.
```
javac -cp %KAFKA_HOME%\libs\* Producer.java
java -cp %KAFKA_HOME%\libs\*;. Producer "topic-1" "First message to topic-1" 
```

After the above command is executed, you can see that data has been produced successfully into the topic. To verify the same, open **Command Prompt** and run the following consumer command to see 
data in `topic-1`.
```
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic topic-1 --from-beginning
```

In VS Code, run the following command on **Terminal** window to produce a second message to `topic-1`.
```
java -cp %KAFKA_HOME%\libs\*;. Producer "topic-1" "Second message to topic-1" 
```
On the cosumer window, you can see this second message. 

#### Producer Callbacks:
We can do Producer callbacks to understand how a producer sends data to Kafka i.e. if the data was correctly produced, where it was produced, its offset value, partition value, etc. 
The callback function used by producer is `onCompletion()` which is implemented for asynchronously handling the request completion and is implemented in the block where producer sends data to Kafka. 

The `onCompletion()` method expects two arguments – `RecordMetadata` _(metadata of record regarding partition and offset)_ and `Exception` _(two possible exceptions – Retirable exception which indicates the 
message may be sent and Non-retirable exception which indicates message will never be sent)_.

In `Producer.java` file, modify the `producer.send()` line with the below code to display `recordMetadata` output once the record is published into Kafka.
```
        // Send producer record
        producer.send(producerRecord, new Callback() {
            //Record metadata on completion
            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                if (e == null) {
                    System.out.println("Data produced successfully with the following metadata");
                    System.out.println("\t Topic: " + recordMetadata.topic());
                    System.out.println("\t Partition: " + recordMetadata.partition());
                    System.out.println("\t Offset: " + recordMetadata.offset());
                    System.out.println("\t Timestamp: " + recordMetadata.timestamp());
                }
                else {
                    System.err.println("Exception while producing data: " + e);
                }
            }
        });
```
Note that the above code will automatically import `org.apache.kafka.clients.producer.Callback` class.

Then compile and run `Producer.java` program with these commands:
```
javac -cp %KAFKA_HOME%\libs\* Producer.java
java -cp %KAFKA_HOME%\libs\*;. Producer "topic-1" "Third message to topic-1" 
```
 
#### Produce Data with Same Key:
When a same key is used to produce messages, those will go into same partition.

In VS Code, modify `Producer.java` code with the following lines:
```
import java.util.Properties;
import java.util.concurrent.ExecutionException;

import org.apache.kafka.clients.producer.Callback;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;
import org.apache.kafka.common.serialization.StringSerializer;

public class Producer {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        // Create instance for properties to access admin configs
        Properties properties = new Properties();

        // Assign bootstrap servers, key and value serializer config values
        //properties.put("bootstrap.servers", "localhost:9092");
        //properties.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        //properties.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,StringSerializer.class.getName());
        properties.setProperty(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,StringSerializer.class.getName());

        // Create instance for Kafka Producer
        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(properties);

        // Generate producer records iteratively
        for (int i = 0; i < 10; i++) {
            String topic = "topic-1";
            String key = "id_"+ Integer.toString(i);
            String value = "Message " + Integer.toString(i) + " sent";
            ProducerRecord<String,String> producerRecord = new ProducerRecord<String,String>(topic, key, value);

            // Send producer record
            producer.send(producerRecord, new Callback() {
                //Record metadata on completion
                public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                    if (e == null) {
                        System.out.println("Produced the following data: ");
                        System.out.println("\t Key: "+producerRecord.key() + ", Value: "+producerRecord.value());
                        System.out.println("Successfully recieved the following metadata:");
                        System.out.printf("\t Topic: %s, Partition: %d, Offset: %d, Timestamp: %d \n",
                        recordMetadata.topic(), recordMetadata.partition(), recordMetadata.offset(), recordMetadata.timestamp());
                    }
                    else {
                        System.err.println("Exception while producing data: " + e);
                    }
                }
            }).get(); //sending synchronous data forcefully  
        }

        // Flush the producer data
        producer.flush();

        // Close the producer object
        producer.close();
    }
}
```
Then compile and run `Producer.java` program with these commands:
```
javac -cp %KAFKA_HOME%\libs\* Producer.java
java -cp %KAFKA_HOME%\libs\*;. Producer 
```
 
After the above command is executed, you can see that all messages are going into partition 1 since we used the same key named `key_0`.

#### Produce Data with Different Keys:
The key in Kafka helps to uniquely identify a partition from other partitions. We can send synchronous messages to Kafka forcefully using `get()` method of `ProducerRecord.send()` method.

In VS Code, modify `Producer.java` code with the highlighted line:
```
        // Generate producer records iteratively
        for (int i = 0; i < 10; i++) {
            String topic = "topic-1";
            String key = "id_"+ Integer.toString(i);
            String value = "Message " + Integer.toString(i) + " sent";
            ProducerRecord<String,String> producerRecord = new ProducerRecord<String,String>(topic, key, value);
```
Then compile and run `Producer.java` program with these commands:
```
javac -cp %KAFKA_HOME%\libs\* Producer.java
java -cp %KAFKA_HOME%\libs\*;. Producer 
```
After the above command is executed, you can see that records are distributed across partitions.

### 5.4. Create Consumer:
Until now, we create producer to send messages to Kafka cluster. Now, let us create a consumer to consume messages from Kafka cluster.

Apache Kafka provides the `consumer` API in which we need to start with using `KafkaConsumer` class which implements `Consumer` class to establish a connection to Kafka cluster and consume data. 
* First, we should instantiate the `Consumer` class by providing a set of key-value pairs as configuration properties. Some of the required properties are `bootstrap.servers` (list of host/port pairs), 
  `key.deserializer`(deserializer class for key) and `value.deserializer` (deserializer class for value).

  Other commonly used properties are `group.id` (unique identifier for a consumer group), `enable.auto.commit` (if set to true, then it commits offset value automatically to Zookepeer, otherwise not committed), 
  `auto.commit.interval.ms` (The frequency of auto committing consumed offsets to Zookeeper), `auto.offset.reset` (this is needed when no initial offset is present of current offset does not exist on server), 
  
  For the complete list of consumer properties, refer to [Apache Kafka Consumer Configs Documentation](https://kafka.apache.org/documentation.html#consumerconfigs). 

**Note:** In the `Properties` object, we can set properties `bootstrap.servers`, `key.deserializer` and `value.deserializer` directly or by calling the respective `BOOTSTRAP_SERVERS_CONFIG`, 
`KEY_DESERIALIZER_CLASS_CONFIG` and `VALUE_DESERIALIZER_CLASS_CONFIG` variables in `ConsumerConfig` class.

* The `KafkaConsumer` class provides `subscribe()` method to subscribe to a given topic in Kafka cluster. 

* The `poll()` method of `KafkaConsumer` class allows to fetch data from topic partitions. This returns error if topics are not subscribed before polling.

* It is important to close the consumer using `close()` methods provided by `consumer` object to avoid any resource leaks.

In VS Code, create a new filed named `Consumer.java` and enter the following code:
```
import java.time.Duration;
import java.util.Arrays;
import java.util.Properties;

import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.serialization.StringDeserializer;

public class Consumer {
    public static void main(String[] args) {
        // Create instance for properties to access admin configs
        Properties properties = new Properties();

        // Assign bootstrap servers, key and value deserializer and other config values
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,StringDeserializer.class.getName());
        properties.put(ConsumerConfig.GROUP_ID_CONFIG,"consumer-grp1");
        properties.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG,"earliest");  
        //properties.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG,"true");
        //properties.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG,"1000");

        // Create instance for Kafka Consumer
        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(properties);

        // Subscribe topic
        consumer.subscribe(Arrays.asList("topic-1"));
        
        // Polling for records
        while(true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
            for (ConsumerRecord<String,String> record : records) {
                System.out.printf("Topic: %s, Partition: %d, Offset: %d, Key: %s, Value: %s\n",
                record.topic(),record.partition(),record.offset(),record.key(),record.value());
            }
        }
        //consumer.close();
    }
}
```
After the above code is entered, click on **+** button on **Terminal** window and select **Command Prompt** where you need to run the following commands to compile the `Consumer.java` file and run it.
```
javac -cp %KAFKA_HOME%\libs\* Consumer.java
java -cp %KAFKA_HOME%\libs\*;. Consumer 
```	

You can see that it keeps polling the topic `topic-1` every 100 milliseconds (i.e. 0.1 second) and displays data fetched from Kafka. Although we set `auto.offset.reset` configuration to `earliest`, 
it works as `latest` and consumes the latest data produced to topic after the consumer was run (somehow, earliest offset is not working here). 
Enter **Ctrl + C** to exit out of consumer application. 

To take the advantage of **Consumer group functionality**, open two command prompts and navigate to the location where `Consumer.java` file exists and run it:
```
D:
cd D:\Projects\Java\Kafka
java -cp %KAFKA_HOME%\libs\*;. Consumer 
```

Open another command prompt and navigate to the location where `Producer.java` file exists and run it to produce some data into Kafka topic:
```
D:
cd D:\Projects\Java\Kafka
java -cp %KAFKA_HOME%\libs\*;. Producer 
```
On the producer terminal, you can see that it has produced few messages into partition 1, few messages into partition 2 and few into partition 3.

On the first consumer terminal, you can see that it read few messages from partition 1 and on the second consumer terminal, you can see that it read few messages from partitions 2 and 3.
<br/>
<br/>

## 6. Spark Streaming with Kafka:
Now, we will create a simple application in Java using Spark and integrate with Kafka to consume messages from a topic and display the frequency of words in messages. 

If you do not have Spark installed already, I would suggest you to install it by referring 
[these steps](https://github.com/srimarrivada/BigData-Installation-on-Windows/blob/main/Install%20Apache%20Spark%203.5.1%20on%20Windows%2010%20and%20Integrate%20with%20Jupiter%20Notebook.md).

To create this project, we need an IDE tool such as **VS Code**, a build tool such as **Apache Maven** and **JDK 1.8** or higher. 

**Apache Maven** is a project management and build tool which is designed based on the concept of project object model (POM). Maven provides developers to manage builds, documentation, reporting, dependencies, 
SCMs, releases, repositories, distribution, mailing list etc.

Three important terminologies that we should be aware of while using Maven is **groupId** _(to uniquely identify the project or application group)_, **artifactId** _(a name of the project file to define 
the artifact's location within the repository)_ and **Version** _(version number of the project or application created by user)_.

If you do not have Maven installed already, then install the latest `apache-maven-x.x.x-bin.zip` file from the [official Apache Maven website](https://maven.apache.org/download.cgi) 
and set the maven install `bin` location in the `PATH` environment variable.
<br/>
<br/>

### 6.1. Verify Spark Version:
It is important to know the installed spark version so that the respective version of Spark Streaming API can be used to create our application.  
Open **Command Prompt** and run the following command:
```
spark-shell --version
```
After executing the above command, you can see the installed Spark version along with Scala version _(In my machine, Spark 3.5.1 with Scala 2.12.18 was installed)_.

### 6.2. Start Spark Services:
Next, start the Spark Master and Worker services to access the Spark cluster.

**Spark Master:**  
Open a new **Command Prompt** and run the following command to start the **Spark Master** service:
```
spark-class org.apache.spark.deploy.master.Master
```

After executing the above command, you can see that Spark master is started at `spark://<ipaddress>:7077` and MasterWebUI is started at http://localhost:8080/.

**Spark Worker:**  
Open a new **Command Prompt** and run the following command _(Make sure to replace `<ipaddress>` with IP address mentioned in your Spark Master URL)_ to start the **Spark Worker** service:
```
spark-class org.apache.spark.deploy.worker.Worker spark://<ipaddress>:7077
```

After executing the above command, you can see that Spark worker is started at `<ipaddress>:22732` with available number of cores and RAM running on the machine and 
WorkerWebUI is started at http://localhost:8081/.

### 6.3. Create Maven Java Project:
Next, we will create a Maven java project in **VS Code** application.

In **VS Code**, open **Command Palette** from **View** menu or press **Ctrl + Shift + P**:
* Type `create java` command and choose **Java: Create Java Project** option.
* Choose **Maven** as build tool. 
* Select **Archetype** as **No Archetype** to create a basic maven project. 
* Enter the **group id** as `com.spark.kafka`
* Enter the **artifact id** as `enter kafka-sparkstreaming`.

Then, it asks you to choose the destination location where you want this project to be created _(I am choosing `D:\Projects\Java\Kafka_Spark` as my location)_ and open that destination folder.

When the destination folder is opened, you can see that java extension got activated and a `Main.java` file is available by default under `kafka-sparkstreaming` -> `src` -> `main` -> `java\com\spark\kafka` 
folder and a `pom.xml` file is created under `kafka-streaming` folder.
<br/>
<br/>

Note that Apache Spark provides `spark-streaming` and `spark-streaming-kafka-0-10` libraries to establish the integration between Kafka and Spark that we will use in our application.  
Go to `pom.xml` under `kafka-streaming` folder in VS Code:
* Add dependencies to pull `spark-streaming` and `spark-streaming-kafka-0-10` from Maven repository.
* Add `maven-jar-plugin` to create our application jar file.
* Ensure `maven.compiler.source` and `maven.compiler.target` properties are set to **1.8** version since we are using JDK 1.8 compiler.

The overall `pom.xml` should look like:
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>


    <groupId>com.spark.kafka</groupId>
    <artifactId>kafka-sparkstreaming</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-streaming -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming_2.12</artifactId>
            <version>3.5.1</version>
            <scope>provided</scope>
        </dependency>
        
        <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-streaming-kafka-0-10 -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming-kafka-0-10_2.12</artifactId>
            <version>3.5.1</version>
        </dependency>
    </dependencies>
    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <artifactId>maven-jar-plugin</artifactId>
                    <version>3.0.2</version>
                    <configuration>
                        <archive>
                            <manifest>
                                <addClasspath>true</addClasspath>
                                <classpathPrefix>lib/</classpathPrefix>
                                <mainClass>com.spark.kafka.WordCount</mainClass>
                            </manifest>
                        </archive>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
</project>
```
Note that I am using `spark-streaming_2.12` and `spark-streaming-kafka-0-10_2.12` dependencies of **Spark 3.5.1** in the above POM since I have Spark 3.5.1 with Scala 2.12 version installed in my machine. 
If you have different Spark version installed, then update the version accordingly.

Once you save the POM file, it asks you to sync the Java class path and click **Yes** to confirm.
<br/>
<br/>

Before proceeding further, make sure Kafka services are up and running. If not running already, open 4 Command Prompts in **Administrator** mode and run the following commands to start **Zookeeper** first 
and then 3 Kafka brokers:
```
zookeeper-server-start.bat %KAFKA_HOME%\config\zookeeper.properties
```
```
kafka-server-start.bat %KAFKA_HOME%\config\server.properties
```
```
kafka-server-start.bat %KAFKA_HOME%\config\server-one.properties
```
```
kafka-server-start.bat %KAFKA_HOME%\config\server-two.properties
```

## 6.4.	Create Spark Application:
Now, let us create a simple Spark application that consumes live streaming data from a Kafka topic and displays count of each word in the consumed messages.

**Apache Spark Streaming** API enables scalable, high-throughput, fault-tolerant stream processing of live data streams. Using this API, we can ingest live data from many sources such as **Kafka**, **Twitter**, 
**Flume** etc., process it using complex algorithms and sync the processed data into many systems such as file systems, databases, live dashboards etc. 

We begin with `SparkConf` API that allows us to set configuration properties as key-value pairs for a Spark application.
* First, we should create a `SparkConf` object and set our application name using `setAppName()` method. This helps us to track our application status on the Spark Master.
  `SparkConf` API provides other methods such as `setMaster()` to set Spark master URL, `set()`, `get()`, etc. To know more details of Spark properties,
  read [Apache Spark Properties Documentation](https://spark.apache.org/docs/latest/configuration.html#spark-properties).

* Next, create a `JavaStreamingContext` object which is the main entry point for Spark Streaming functionality. While creating this object, we need to specify `SparkConf` and `Duration` parameters.
  The `Duration` parameter tells Spark for how much time it has to fetch the incoming data so that Spark divides the incoming live data into batches of given duration, processes it and generates the final result
  in batches. For example, if the given Duration is 30 seconds, then it would fetch data from source for every 30 seconds.  
  _**Note:**_ `JavaStreamingContext` is a Java friendly version of `StreamingContext`.

* Next, create `kafkaParams` map object and set configuration properties for Kafka as key-value pairs.  We need to specify properties such as `bootstrap.servers`, `key.deserializer`, `value.deserializer`,
  `group.id`, `auto.offset.reset`, `enable.auto.commit` for our Kafka cluster.

* We will use `KafkaUtils` API to connect to Kafka cluster through Spark Streaming. You can find this API in `spark-streaming-kafka-0-10` library. `KafkaUtils` API has `CreateDirectStream()` method which is
  used to create an input stream that pulls messages from Kafka topics. This method has different signatures with different types of arguments but we use a signature of passing `JavaStreamingContext`,
  `LocationStrategy` and `ConsumerStrategy` as parameters to the method and it returns `ConsumerRecord` as `JavaInputDStream`.
  * `LocationStrategy` is a way of distributing processing of Kafka input stream of messages across Spark executors to achieve optimum performance. The most preferred `LocationStrategy` is `PreferConsistent`
     which distributes Kafka partitions evenly across available Spark executions. When your Spark executors are located on the same hosts as your Kafka brokers, then choose `PreferBrokers` strategy.
     When you notice a significant skew in load or load is uneven across partitions, then use `PreferFixed` strategy which allows to map specific topic partition to specific host.  
     _**Note:**_ `PreferConsistent`, `PreferBrokers`, `PreferFixed` are available as methods in `LocationStrategies` factory object.
  *	`ConsumerStrategy` allows users how to configure Kafka consumers in Spark Streaming. Spark provides three consumer strategies - `Subscribe` which allows to subscribe to a fixed collection of topics,
     `SubscribePattern` which allows to use regex to specify topics of interest and `Assign` which allows to specify a fixed collection of topic partitions.  
    _**Note:**_ `ConsumerStrategies` factory object provides `Subscribe`, `SubscribePattern` and `Assign` as methods to use.  
  
  We will use `ConsumerStrategies.Subscribe()` method which requires us to pass `topics` collection and `kafkaParams` arguments.

* Next, we perform a series of operations on `JavaInputDStream` to obtain word frequencies in the messages.
  * Using `map()` function, call `value()` method of `JavaInputDStream` `ConsumerRecord` to retrieve the actual message in a Kafka record and return `JavaDStream` messages.
  * Using `flatmap()` function, split each message line in the above `JavaDStream` by a space which returns an array of `JavaDStream` words. 
  * Using `mapToPair()` function, map the each word in the above `JavaDStream` to a tuple containing a word and 1 which returns `JavaPairDStream` object.
  * Finally, apply the `reduceByKey()` function on the above `JavaPairDStream` using lambda expression that counts by each word and returns `JavaPairDStream` object.

* The processed `JavaPairDStream` contains collection of elements as javaPairRDDs in batches. We can iterate over this `JavaPairDstream` to display each word and its number of occurrences.

* Since this is a streaming application, we need to keep this running by calling `start()` and `awaitTermination()` methods of `JavaStreamingContext`.

In VS Code, create a new file named `WordCount.java` and enter the following code:
```
package com.spark.kafka;
import java.util.Arrays;
import java.util.Collection;
import java.util.HashMap;
import java.util.Map;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.apache.spark.SparkConf;
import org.apache.spark.streaming.Durations;
import org.apache.spark.streaming.api.java.JavaDStream;
import org.apache.spark.streaming.api.java.JavaInputDStream;
import org.apache.spark.streaming.api.java.JavaPairDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;
import org.apache.spark.streaming.kafka010.ConsumerStrategies;
import org.apache.spark.streaming.kafka010.KafkaUtils;
import org.apache.spark.streaming.kafka010.LocationStrategies;

import scala.Tuple2;

@SuppressWarnings("deprecation")
public class WordCount {
    public static void main(String[] args) throws InterruptedException {
        // Create instance to access Spark streaming
        SparkConf sparkConf = new SparkConf();
        sparkConf.setAppName("Kafka Spark Streaming");

        // Create map object to set kafka parameters
        Map<String, Object> kafkaParams = new HashMap<>();
        // Assign bootstrap servers, key and value serializers, group id and other config values
        kafkaParams.put("bootstrap.servers", "localhost:9092");
        kafkaParams.put("key.deserializer", StringDeserializer.class);
        kafkaParams.put("value.deserializer",StringDeserializer.class);
        kafkaParams.put("group.id","word-count-streaming");
        kafkaParams.put("auto.offset.reset","earliest");
        kafkaParams.put("enable.auto.commit",false);

        // Create collection object and assign list of existing Kafka topics to read
        Collection<String> topicList = Arrays.asList("topic-1");

        // Create java streaming context using spark config with duration of 2 seconds
        JavaStreamingContext streamingContext = new JavaStreamingContext(sparkConf, Durations.seconds(30));

        // Get consumer record stream using KafkaUtils API with streaming context and subscribed to given Kafka topics
        JavaInputDStream<ConsumerRecord<String,String>> consumerRecordStream = 
        KafkaUtils.createDirectStream(streamingContext, LocationStrategies.PreferConsistent(),
                                      ConsumerStrategies.Subscribe(topicList, kafkaParams));
        
        // Take value from consumer record stream which has actual data
        JavaDStream<String> messages = consumerRecordStream.map(record -> record.value());

        // Split each record by a space and map each word to 1 and apply reduceByKey() to get count of words in a stream
        JavaDStream<String> words = messages.flatMap(line -> Arrays.asList(line.split("\\s+")).iterator());
        JavaPairDStream<String,Integer> wordsMap = words.mapToPair(word -> new Tuple2<> (word,1));
        JavaPairDStream<String,Integer> wordCounts = wordsMap.reduceByKey((i1,i2) -> (i1 + i2));

        // For each RDD in a stream, display the word and it's number of occurances
        wordCounts.foreachRDD(javaPairRdd -> {
                Map <String, Integer> wordCountMap = javaPairRdd.collectAsMap();
                System.out.println("Count of words from latest consumer read:");
                for(String key: wordCountMap.keySet()) {
                    System.out.println("\t Word: " + key + ", " + "Number of occurances: " + wordCountMap.get(key));
                }
            }
        );

        // Start the streaming process and wait until the application is terminated
        streamingContext.start();
        streamingContext.awaitTermination();
    }
}
```

After the above code is entered, expand **Maven** section, right-click on `kafka-sparkstreaming` artifact and select **Run Maven Commands** and choose **clean**. This executes `mvn clean` command on the 
terminal and displays **BUILD SUCCESS** message.

Next, right-click on `kafka-sparkstreaming` artifact under **Maven** section and select **Run Maven Commands** and choose **package**. This executes `mvn package` command on the terminal and builds a jar 
file called `kafka-sparkstreaming-1.0-SNAPSHOT.jar` which is located under `target` folder in `kafka-sparkstreaming` folder.

### 6.5. Execute Spark Application:
Now that our application jar file is ready, we need to deploy our application using `spark-submit` script that comes with Spark installation.

In VS Code, click on **+** button on **Terminal** window and select **Command Prompt** where you need to run the following command to deploy the application in client mode on Spark cluster.  
In the below command, make sure to replace `spark://<ip_address>:7077` with your Spark Master URL that is displayed on Spark Master UI at http://localhost:8080/
```
%SPARK_HOME%\bin\spark-submit.cmd --master spark://<ip_address>:7077 --packages org.apache.kafka:kafka-clients:3.8.0,org.apache.spark:spark-streaming-kafka-0-10_2.12:3.5.1 --class com.spark.kafka.WordCount kafka-sparkstreaming\target\kafka-sparkstreaming-1.0-SNAPSHOT.jar
```

After executing the above command, you can see **INFO** logging on the console which says it is downloading necessary packages, connecting to spark cluster, integrating with Kafka cluster to read 
from Kafka topic `topic-1`.  Then it displays the frequency of words in the messages.

As this is a streaming application, it keeps reading data from Kafka topic at `30` seconds batch interval (as specified in our application) and displays frequency of words for every batch until the 
application is terminated. You can enter **Ctrl + C** to terminate this application manually.

_**Note:**_  
By default, Spark displays INFO logging on the console. Follow these steps to disable it:
* Go to `%SPARK_HOME%\conf` folder, take the copy of `log4j2.properties.template` file and rename as `log4j2-spark.properties`.
* Open `log4j2-spark.properties` and change the `rootLogger.level` value from `info` to `warn` and save it.
* On the VS Code terminal, execute the below `spark-submit` command. Make sure to replace `spark://<ip_address>:7077` with your Spark Master URL displayed on Spark Master UI and also put the absolute 
  path of your SPARK_HOME in `configurationFile` path in the below command:
  
  ```
  %SPARK_HOME%\bin\spark-submit.cmd --master spark://<ip_address>:7077 --driver-java-options "-Dlog4j.configurationFile=file:///D:/ProgramFiles/Spark/spark-3.5.1-bin-hadoop3/conf/log4j2-spark.properties" --packages org.apache.kafka:kafka-clients:3.8.0,org.apache.spark:spark-streaming-kafka-0-10_2.12:3.5.1 --class com.spark.kafka.WordCount kafka-sparkstreaming\target\kafka-sparkstreaming-1.0-SNAPSHOT.jar
  ```

**Congratulations!! You have successfully installed Apache Kafka with single broker and multi-broker configuration and created a streaming application by integrating Kafka with Spark in Windows 
operating system.**
