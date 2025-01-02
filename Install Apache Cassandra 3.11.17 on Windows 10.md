# Install Apache Kafka 3.8 on Windows 10 and Integrate with Spark
Apache Cassandra is an open-source, highly scalable and distributed NoSQL (Not only SQL) database management system that is designed to handle large amounts of structured and unstructured data distributed across multiple commodity servers.

Apache Cassandra is initially developed at Facebook with a combination of distribution storage and replication model from Amazon Dynamo and column-family data model from Google Bigtable. It has a distributed architecture where large volumes of structed and unstructured data can be stored on multiple commodity hardware machines which provides high availability with no single point of failure, making it a popular choice for enterprises needing robust data solutions.

Apache Cassandra follows decentralized, peer to peer model. Unlike traditional databases that use master-slave architecture, Cassandra operates on peer-to-peer model which is nothing but all nodes in Cassandra cluster are identical, with no master nodes and each node communicates with other nodes directly, ensuring no bottlenecks and single point of failure.

The key components of Cassandra include Node, Data Center, Cluster, Commit log, Mem-table, SSTable. It is important to understand these terminologies in Cassandra.
•	Node: It is the basic component in Cassandra. A node is nothing but a machine where data is actually stored
•	Data Center: It is a collection of many nodes.
•	Cluster: It is a collection of multiple data centers
•	Commit Log: It is the first entry point while data to Cassandra. Data is first written to commit log which is a durable write-ahead log on disk. This helps in data recovery and sync in case of any crash in nodes.
•	Mem-table: After writing data to commit log, data is stored in memtable which is an in-memory data structure.
•	SSTable: Once the mem-table reaches a certain size, then data is flushed to disk and is stored in a SSTable (Sorted String table), an immutable data file.
•	Consistency levels: Cassandra allows users to choose consistency level for their read and write operations, balancing between consistency and availability. 
•	Partitioning: Cassandra uses partitioning concept to distribute data across cluster. It hashes partition key of a row with a consistent hashing mechanism and determines which node will store that row.
•	Replication Strategy: Based on the replication factor configured, Cassandra replicates partitions across multiple nodes to ensure data availability and fault tolerance. Cassandra follows two replication strategies – SimpleStrategy (allows to specify single replication factor to copy data across cluster) NetworkTopologyStrategy (allows to specify replication factor for each data center in the cluster) 

Note:
The latest release of Apache Cassandra from 4.0+ does not support the direct installation on Windows operating system. If you would like to use Apache Cassandra 4.0+ version, then go with Docker image or install it on Linux operating system.

This document provides instructions to install Apache Cassandra 3.11.9 with node and multi-node cluster configuration on Windows Operating system. 
You can also go through [this PDF document](/doc/Install%20Apache%20Kafka%203.8%20on%20Windows%2010.pdf) for installation steps along with screenshots.  
<br/>

## 1. Prerequisites:
The following prerequisites need to be installed before running Cassandra.

1.	File Archiver: Any file archiver such as 7zip or WinRAR is needed to unzip the downloaded Spark binaries. 7zip can be downloaded from the 7zip Downloads website and WinRAR can be downloaded from the RAR lab Downloads website.

2.	JRE 8: Cassandra 3.11 requires Java 8 runtime environment supporting up to JDK 1.8_251 release. You can download the JDK 8u251 release from Oracle Java Archive Downloads website. For the complete JDK installation steps, look at here. 

Verify the installed Java version using the below command:

java -version

 

3.	Python 2.7: Cassandra 3.11 requires Python 2.7 to be installed to be able to use cqlsh tool. You can install Python 2.7.8 release from the official Python Downloads website and set the python install location in PATH environment variable.

Verify the installed Python version using the below command:

python --version

 

It displays that Python 2.7.8 version is installed.


3.	Install Cassandra:
Let us see the step by step procedure to install Apache Cassandra in Windows.

3.1.	Download Cassandra Binaries:
You can get the stable release from the official Apache Cassandra Downloads website. At the time of this document preparation, the most recent stable release is 4.1.7 which cannot be used on Windows system directly. So, let us go the Cassandra Archive location and download 3.11.17 release.

On the Cassandra Archive 3.11.17 page, click on apache-cassandra-3.11.17-bin.tar.gz link which downloads the file into your Downloads folder in your machine.

 

After the binary file is downloaded, unpack it using any file archiver (7zip or WinRAR) utility as below:

•	Choose the installation directory in your machine and copy apache-cassandra-3.11.17-bin.tar.gz file to that directory. Here, we are choosing Cassandra installation directory as D:\ProgramFiles\Cassandra.

 

•	Right click on apache-cassandra-3.11.17-bin.tar.gz and choose 7-Zip -> Extract Here option which extracts a new packed file apache-cassandra-3.11.17-bin.tar.

 

•	Next, unpack apache-cassandra-3.11.17-bin.tar file using 7zip utility.

 

•	The tar file extraction may take few minutes to finish. After finishing, you see a folder named apache-cassandra-3.11.17-bin which consists of Kafka binaries and libraries.

 

3.2.	Set up Environment variables:
After installing pre-requisites and Cassandra binaries, we should configure the below environment variables defining Java and Cassandra default paths.

•	JAVA_HOME: This is the JDK installation directory path in the machine (in my machine, it is D:\ProgramFiles\Java\jdk1.8.0_251). Ignore it if this is already done.

•	CASSANDRA_HOME: This is the Cassandra installation directory path in the machine (in our case, it is D:\ProgramFiles\Cassandra\apache-cassandra-3.11.17)

These variables need to be added to either User environment variables or System environment variables depending on Cassandra configuration needed for a single user or for multiple users.

In this tutorial, we will add User environment variables since we are configuring Cassandra for a single user. If you would like to configure Cassandra for multiple users, then define System environment variables.





In the Windows search bar, start typing “environment variables” and select the first match which opens up System Properties dialog.

 

On the System Properties window, press Environment Variables button.

 

In the Environment Variables dialog, click on New under User variables section.

 

Add JAVA_HOME variable and press OK.

 

Click on New again and add CASSANDRA_HOME variable and press OK. 

 

Now, we will update PATH variable to add Java, and Cassandra binary paths.

Select PATH variable under User Variables and press Edit button.

 

Press New and add the following values and press OK.
%JAVA_HOME%\bin 
%CASSANDRA_HOME%\bin

 

Press OK again to apply environment variable changes and close window.

 

3.3.	Verify Cassandra Installation:
Open Windows PowerShell and run the following command to verify if Cassandra is installed properly: (you cannot do this from Command Prompt and must use Windows PowerShell)

cassandra -v

Note:
While executing the above script, you may encounter an error cassandra : File D:\ProgramFiles\Cassandra\apache-cassandra-3.11.17\bin\cassandra.ps1 cannot be loaded because running scripts is disabled on this system, as shown below. This error occurs when you do not have permissions to execute the PowerShell script.

 
To resolve the above error, run the below command to set the execution policy to ‘Remotesigned’ for the current user.

powershell Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

 

Now, you should be able to verify the Cassandra version installed by running this command

cassandra -v

 

Here, it shows 3.11.17 version which indicates that Cassandra has been installed successfully.

Note:
It is necessary to have PowerShell execution policy set to either ‘RemoteSigned’ or ‘Unrestricted’ to be able to run Cassandra commands from Windows PowerShell prompt in Windows system. Going forward, we will execute Cassandra commands from Command Prompt itself and so let us revert the execution policy that we have set earlier by executing the below command in Windows PowerShell.

powershell Set-ExecutionPolicy Undefined -Scope CurrentUser

 

4.	Configure Cassandra:
When Cassandra is used on a single node, there is no need to make any changes to the default configuration. If you would like to setup a Cassandra cluster with multiple nodes, it is important to know the configuration details. The key configuration files used in Cassandra are available under %CASSANDRA_HOME%\conf folder.

1.	cassandra.yml – It is the primary config file for each node instance to configure the cluster name, IP address, directory paths, etc.
2.	cassandra-env.sh – Environment file to set java environment settings such as MAX_HEAP_SIZE.
3.	Cassandra-rackdc.properties – Config file used to set the rack and data center to which the node belongs to.
4.	Cassandra-topology.properties – Config file used to specify the IP address for racks and data centers in the cluster.

Though we are using Cassandra with single node, let us see how some configuration changes can be made for cluster setup.

Open cassandra.yml from %CASSANDRA_HOME%\conf directory and set the following properties:
•	cluster_name: Name of the cluster. By default, it is set to Test Cluster. You can change it to the desired name, for instance, CassandraDBCluster. Make sure that all nodes in the cluster use the same cluster_name.
•	num_tokens: Number of tokens. By default, this is set to 256. Any value greater than 1 is treated as virtual node so that token distribution will happen automatically.
•	listen_address: IP address or hostname of the current node to be used by other nodes to connect to this node. By default, it is set to localhost but you can change it to the internal IP address of the node.
•	rpc_address: Internal IP address for thrift client connections. By default, it is set to localhost but you can change it to the internal IP address of the node.
•	seed_provider: List of internal IP addresses of hosts that are deemed contact points. In the config file of every node, we should specify the IP address of seed nodes. By default, it is set to "127.0.0.1" and should be updated with internal IP addresses of all hosts in the cluster.
•	endpoint_snitch: It gives some information about network topology so that Cassandra can efficiently route requests. By default, it is set to SimpleSnatch class which is appropriate for cluster setup with single datacenter. For multi-data center deployments, it is recommended to use GossipingPropertyFileSnitch with which rack and datacenter for the local node are specified in Cassandra-rackdc.properties and propagated to other nodes via gossip. Cassandra supports other types of snitches such as PropertyFileSnitch, Ec2Snitch, Ec2MultiRegionSnitch, RackInferringSnitch, etc. 
•	auto_bootstrap: This configuration is not available in the file and can be added and set to false. This configuration is optional if nodes are added to existing cluster but required when creating a new cluster with no data.

Locate the above settings in cassandra.yml file and modify as below:

cluster_name: 'CassandraDBCluster'
listen_address: 127.0.0.1
rpc_address: 127.0.0.1
seed_provider:
  - class_name: org.apache.cassandra.locator.SimpleSeedProvider
    parameters:
         - seeds: "127.0.0.1"
endpoint_snitch: GossipingPropertyFileSnitch
auto_bootstrap: false
When the endpoint_snitch is set to GossipingPropertyFileSnitch in cassandra.yaml file, it always loads cassandra-topology.properties file if present and so, it is recommended to remove or rename this file in %CASSANDRA_HOME%\conf directory on all nodes.

 

Now, we can assign a datacenter and a rack name of the current node in %CASSANDRA_HOME%\conf\cassandra-rackdc.properties file. By default, dc is set to dc1 and rack is set to rack1. Change these settings if you want to put the node on a different rack in a different datacenter.

In cassandra-rackdc.properties file, modify as below:

dc=Asia
rack=South

 

5.	Start Cassandra:
It is time to start the Cassandra service and test the connection. 

5.1.	Start Cassandra Service:
Open Command Prompt and start Cassandra service using the below command:

cassandra

 
 

On the console, you will see a message “Node /127.0.0.1 state jump to NORMAL” which indicates that the Cassandra instance has started and is up and running. Do not close this command prompt window.

We can also use the jps command to verify Cassandra service:

jps

It should display CassandraDaemon which denotes that Cassandra service is running.

 

5.2.	Verify Node Status:
Now, let us check the status of current node using nodetool utility with the following command:

nodetool status

 

Here, you can see the process status, listening address, tokens, host ID, data center and rack name of the current node.

Check the token distribution of the node using the following command

nodetool ring

 


6.	Configure Multi-Node Cluster:
For the purpose of this documentation, we will create a second Cassandra node in the same system with a different address but in the real-time project, we should configure one node per system in a Cassandra cluster.

6.1.	Extract Cassandra Binaries:
To set up a new Cassandra node, create a new folder where a new instance of Cassandra can be installed. In my case, I created node2 folder under D:\ProgramFiles\Cassandra location.

 


Copy the Cassandra binary file apache-cassandra-3.11.17-bin.tar.gz to the new folder and extract contents of it. 

 

6.2.	Modify Configuration Files:
We should update the following files in the new Cassandra install location to configure the second node.
cassandra.yaml
cassandra-rackdc.properties
cassandra-env.sh

Go to D:\ProgramFiles\Cassandra\node2\apache-cassandra-3.11.17\conf and edit cassandra.yaml file with the following settings:

cluster_name: 'CassandraDBCluster'
listen_address: 127.0.0.2
rpc_address: 127.0.0.2
seed_provider:
  - class_name: org.apache.cassandra.locator.SimpleSeedProvider
    parameters:
         - seeds: "127.0.0.1,127.0.0.2"
endpoint_snitch: GossipingPropertyFileSnitch

 

When the endpoint_snitch is set to GossipingPropertyFileSnitch in cassandra.yaml file, it always loads cassandra-topology.properties file if present and so, it is recommended to remove or rename this file.

 

Now, open cassandra-rackdc.properties file and change the data center and rack name as below. Note that we are putting this new node altogether in a different data center compared to the existing node which is on Asia DC.

dc=North America
rack=US

 

Additionally, it is required to change JMX port number in cassandra-env.ps1 and cassandra.bat files.

Open cassandra-env.ps1 in D:\ProgramFiles\Cassandra\node2\apache-cassandra-3.11.17\conf location and update JMX_PORT value from 7199 to 7299.

 

Open cassandra.bat in D:\ProgramFiles\Cassandra\node2\apache-cassandra-3.11.17\bin location and update Dcassandra.jmx.local.port value from 7199 to 7299.

 

6.3.	Remove Environment Variable:
Whenever we try to start Cassandra, it always refers to the configuration defined in CASSANDRA_HOME environment variable and when this variable is not set, it tries to look for config files available in the current directory.

Since we are configuring the second node on the same machine, we should remove CASSANDRA_HOME environment variable that we set earlier.

Open Environment Variables window and select CASSANDRA_HOME under 
User variables section and click on Delete button. Then press OK to apply environment variable changes and close window.

 


6.4.	Start Cassandra Service:
Open Command Prompt and start Cassandra service by navigating to the location where the second instance was installed:

D:
cd D:\ProgramFiles\Cassandra\node2\apache-cassandra-3.11.17\bin
cassandra

 
 

On the console, you will see a message “Node /127.0.0.2 state jump to NORMAL” which indicates that the Cassandra instance has started and is up and running. Do not close this command prompt window.

We can also use the jps command to verify Cassandra service:

jps

It should display two CassandraDaemon daemons which denotes that 2 instances of Cassandra are running.

 

6.5.	Verify Node Status:
Now, let us check the status of nodes using nodetool utility by running the following commands:

D:
cd D:\ProgramFiles\Cassandra\node2\apache-cassandra-3.11.17\bin
nodetool status

 

Here, you can see that node1 under Asia DC and node 2 under North America DC and both nodes status shows UN which means the node is Up and reporting Normal state.

Run the following nodetool command to get the gossip information of each node:

nodetool gossipinfo

 


7.	CQLSH:
Cassandra provides an interactive tool called Cassandra Query Language Shell (cqlsh) to communicate with Cassandra database and execute queries using Cassandra Query Language (CQL). The CQL is very similar to SQL but suited for JOINless structure.

While using cqlsh, we can specify the IP address and port to connect to a specific Cassandra node with specific username and password. We can use the default user cassandra and password cassandra to connect to database.

Open Command Prompt and run the following commands to launch CQLSH tool connecting to second node in our system

cd D:\ProgramFiles\Cassandra\apache-cassandra-3.11.17\bin
cqlsh 127.0.0.2 9042
or
cd D:\ProgramFiles\Cassandra\apache-cassandra-3.11.17\bin
cqlsh 127.0.0.2 9042 -u cassandra -p cassandra

 

Here, we can see that it is connected to CassandraDBCluster at node address 127.0.0.2:9042

On cqlsh> prompt, use HELP command that lists out all possible commands that can be triggered to interact with Cassandra.







To get more help on each command, use HELP followed by command name. For example, to know more details about SHOW command, run HELP SHOW as shown here






7.1.	Describe Cluster:
Use the below CQLSH command to see the information about the connected Cassandra cluster, such as the cluster name, and the partitioner and snitch in use. When you are connected to a non-system keyspace, also shows endpoint-range ownership information for the Cassandra ring.

DESCRIBE CLUSTER

 

7.2.	View Version:
Use the below CQLSH command to see the version and build of the connected Cassandra instance:

SHOW VERSION

 

Here, you can see that the CQLSH version 5.0.1 and Cassandra 3.11.17 that we installed.

7.3.	Show Host:
To see where the current CQLSH session is connected, run the below command:

SHOW HOST

 

Here, you can see that the current CQLSH instance is connected to CassaandraDBCluster.

7.4.	Create Keyspace:
A keyspace in Cassandra is like database in RDBMS and contains column families, indexes, user defined types, data center awareness, strategy used in keyspace, replication factor, etc.

Let us create a new keyspace named testspace with replication factor 2 using the below command:

CREATE KEYSPACE testspace WITH replication = {'class':'SimpleStrategy', 'replication_factor':2};

 

Cassandra keyspace can be created with two types of strategy declaration:
•	SimpleStrategy: This strategy is used in the case of one data center. In this strategy, the first replica is placed on the selected node and the remaining nodes are placed in clockwise direction in the ring without considering rack or node location.
•	NetworkTopologyStrategy: This strategy is used in the case of more than one data centers. In this strategy, you have to provide replication factor for each data center separately.

Replication Factor is another property to be specified for a keyspace.  Replication factor is the number of replicas of data placed on different nodes.

Use describe command to verify if the new keyspace was created or not:

DESCRIBE KEYSPACES;

 



The use command in Cassandra sets the current working keyspace. Run the following command to set testspace as our working keyspace:

USE testspace;

 

7.5.	Create Table:
In Cassandra, CREATE TABLE command is used to create a table with column families to store data just like table in RDBMS. This command expects the table name, column name, data type and a primary key. The datatypes in Cassandra can be text, int, date, decimal, double, float, set, list, map, etc.

Use this command to create table named student:

CREATE TABLE student (
student_id INT PRIMARY KEY,
student_name TEXT,
student_city TEXT,
student_fees INT
);

 

Use describe command to verify if the table was created or not:

DESCRIBE TABLES;

 


7.6.	Insert Data:
The INSERT command is used to insert data into table columns.

Run the following commands to insert some data into student table:

INSERT INTO student (student_id, student_name, student_city, student_fees)
VALUES (1, 'Rakesh', 'Hyderabad', 5000);
INSERT INTO student (student_id, student_name, student_city, student_fees)
VALUES (2, 'Ramana', 'Bangalore', 7000);
INSERT INTO student (student_id, student_name, student_city, student_fees)
VALUES (3, 'Kranthi', 'Chennai', 4000);

 


7.7.	Read Data:
Use SELECT command to read data from the table.

SELECT * FROM student;

 

SELECT * FROM student WHERE student_id=2;

 

7.8.	Batch Queries:
In Cassandra, the BATCH command is used to execute multiple DML statements such as insert, update, delete, etc. simultaneously. It is very useful when you have to update some column as well as delete some of the existing data.

Run the following batch operations to insert a new record, update the existing record and delete a column data.

BEGIN BATCH
INSERT INTO student (student_id, student_name, student_city, student_fees) VALUES (4, 'Shivani', 'Mumbai', 10000);
UPDATE student SET student_fees = 8000 WHERE student_id = 2;
DELETE student_fees FROM student WHERE student_id=1;
APPLY BATCH;

 

Now the BATCH is applied, we can verify it by using SELECT command:

SELECT * FROM student;

 
	
Use exit; command to exit out of cqlsh.
7.9.	Data Replication:
Now, let us connect to the first node instance to verify if the data is replicated.

cd D:\ProgramFiles\Cassandra\apache-cassandra-3.11.17\bin
cqlsh 127.0.0.1 9042 -u cassandra -p cassandra

 

Run the following commands to see the table data that we created in the second node.

DESCRIBE KEYSPACES;
USE testspace;
DESCRIBE TABLES;
SELECT * FROM student;


 

Some of the generally used commands are described below for your understanding.
8.	NodeTool Utility:
Cassandra provides a nodetool utility which is a command line interface for monitoring Cassandra cluster and performing routine database operations. This utility is commonly used to output a quick summary of the ring and its current state of general health with the status command.

The nodetool utility provides commands for decommissioning a node, running repair, and moving partitioning tokens and for viewing detailed metrics for tables, server metrics, and compaction statistics, etc. 

When you have multi-node cluster, you can provide the node host name and JMX port to connect:

nodetool -h HOSTNAME -p JMX_PORT COMMAND

If a username and password for RMI authentication are set explicitly in the cassandra-env.sh file, then you must specify credentials along with hostname/IP address and port to connect to a specific Cassandra node:

nodetool -h HOSTNAME -p JMX_PORT -u JMX_USERNAME -pw JMX_PASSWORD COMMAND

Open Command Prompt and run the following commands to get node status connecting to pri node in our system:

cd D:\ProgramFiles\Cassandra\apache-cassandra-3.11.17\bin
nodetool -h 127.0.0.1 -p 7199 -u cassandra -pw cassandra status

Use the below command to connect to secondary node in our system:

nodetool -h 127.0.0.1 -p 7299 -u cassandra -pw cassandra status

 

8.1.	Nodetool help:
This is a basic command which lists all the available nodetool commands. This command is useful to learn about the available commands.

nodetool help

 

8.2.	Nodetool describecluster:
This command provides the basic cluster information such as cluster name, endpoint-snitch being used, partitioner and schema versions.

Follow the below syntax to use this command:

nodetool [options] describecluster

 

8.3.	Nodetool status:
This command is useful to monitor the cluster’s health and performance. It can help detect any ongoing anomalies as well as the status of each of the nodes.

Follow the below syntax to use this command:

nodetool <options> status ( -r | --resolve-ip ) -- <keyspace>

 

8.4.	Nodetool ring:
It displays token ring information with the status of each of the ndoes. The token ring is responsible for managing the partitioning of data within the Cassandra cluster. This command is critical if a cluster is facing data consistency issues. By default, nodetool ring displays all nodes that are involved in the ring and tokens that are assigned to each one of them. Optionally, you can specify the keyspace name and table name arguments to filter the output and display information.

Follow the below syntax to use this command:

nodetool ring [keyspace] [table]

 

8.5.	Nodetool gossipinfo:
It provides the gossip protocol related statistics.

Follow the below syntax to use this command:

nodetool [options] gossipinfo

 

8.6.	Nodetool getlogginglevels:
It gives logging levels defined in the database for all areas

Follow the below syntax to use this command:

nodetool [options] getlogginglevels

 

8.7.	Nodetool netstats:
This command provides the network information about the host machine.

Follow the below syntax to use this command:

nodetool [options] netstats

 

8.8.	Nodetool tablestats:
This command provides statistics about one or more tables. The table stats are updated when SSTables change through compaction or flushing. Cassandra uses the metrics-core library to make the output more informative and easier to understand.

Follow the below syntax to use this command:

nodetool [options] tablestats

8.9.	Nodetool cleanup:
This command that is required to remove data from nodes when a new node is added to a cluster and token ring is re-distributed. After cleanup it is recommended to run compact command.

Follow the below syntax to use cleanup command:

nodetool [connection_options] cleanup [-j num_jobs] [--] [keyspace_name table_name [table_name ...]]

8.10.	Nodetool compact:
This command is useful to perform compaction to merge several SSTables (data files in Cassandra) and keep the resultant SSTable with the latest updated data. 

When a data update or delete operation is triggered, Cassandra does not overwrite or delete the data. In case of updates, a different timestamped version of updates is maintained and in case of deletes, the data is marked for deletion as tombstones and then the latest version of data is obtained post the merge of the SSTables. Therefore, it is important to perform compactions on a periodic basis in order to keep the cluster healthy.
 
Follow the below syntax to use compact command:

nodetool [options] compact [(-et <end_token> | --end-token <end_token>)] 
[(-s | --split-output)] [(-st <start_token> | --start-token <start_token>)] [--] [<keyspace> [<tables>...]]
[--user-defined] <relative_path_to_SSTable file>

8.11.	Nodetool decommission:
It decommissions the node where this command is executed, and the data of the node is streamed to the next node in the ring. It is one of the important commands that need to be executed when the cluster needs to be scaled to ensure no data loss.

Follow the below syntax to use decommission command:

nodetool [options] decommission

8.12.	Nodetool drain:
It flushes all memtables (temporary tables usually on the heap) to the SSTables on disk. Once the command is executed, the node stops listening for connections from clients and other nodes. The node is marked as DS (Down/Stopped) in the cluster in the nodetool status command output. This command is usually run before any maintenance activities or when upgrading a node to a newer version of Cassandra.

Follow the below syntax to use drain command:

nodetool [options] drain

8.13.	Nodetool garbagecollect:
It performs single SSTable compactions to eliminate updates or logically deleted data (Tombstones).

For each SSTable, the command creates a new SSTable with unnecessary data cleaned out.
By default, garbagecollect removes rows or partitions that have been deleted or overwritten with newer data. It can also remove deleted or overwritten cell values when -g argument is specified. This command can eliminate expired tombstones which are older than gc_grace_seconds, but not fresh ones.

Note that garbagecollect with -g cell option incurs very high Disk I/O and hence it should be ensured that enough disk space is available.
Follow the below syntax to use this command:

nodetool options garbagecollect [--] keyspace_name table_name

8.14.	Nodetool join:
It adds a new node to the cluster. When this command is executed, the new node will start to communicate with other nodes in the cluster and receive data from them. It is important to ensure that the new node has the same version of Cassandra as the existing nodes in the cluster.

Follow the below syntax to use this command:

nodetool <options> join

8.15.	Nodetool removenode:
This command is useful when a node is no longer needed or when a node has failed and needs to be replaced or removed. Before removing the dead node, nodetool decommission command must be run first on the node being removed and then nodetool removenode command should be executed on the live node in the cluster.

When a node is removed, Cassandra redistributes the data that was stored on that node to the remaining nodes in the cluster. After the data has been redistributed, the node being removed will be marked as “removed” and will no longer participate in the cluster.

It is important to ensure that all data has been successfully redistributed before decommissioning or shutting down the node being removed. This can be checked using nodetool status and monitoring the “UN” (up and normal) status of all nodes in the cluster. 
It is recommended to perform a full repair of the cluster after removing a node to ensure data consistency. This can be done using nodetool repair command.

Follow the below syntax to use nodetool removenode command:

nodetool [connection_options] removenode -- <status> | <force> | <ID>

8.16.	Nodetool assasinate:
This command is used to assassinate a node and should be performed when the nodetool removenode command fails.

Follow the below syntax to use nodetool assasinate command:

nodetool [options] assassinate <ip_address>

8.17.	Nodetool repair:
Since frequent data deletions and downtime in nodes may lead to data inconsistencies, nodetool repair ensures data consistency across all nodes in the cluster. It works by comparing the data on each node with the data on other nodes and resolving any inconsistencies. This is done by constructing a merkle tree whose leaves are the hashes of the individual keys.

Note that while nodetool repair is running, it can cause increased network traffic and disk I/O on the nodes in the cluster. This can impact the performance of other applications running on the same nodes.  It can also cause temporary data unavailability during the repair process which can be mitigated by running repairs during off-peak hours or using incremental repair (new data since last repair) instead of full repair (complete data).

If nodetool repair is interrupted or fails for any reason, it can leave the cluster in an inconsistent state and so it is important to monitor the progress of nodetool repair and ensure that it completes successfully.

Follow the below syntax to use this command:

nodetool repair [-full|-inc]



Congratulations!! You have successfully installed Apache Cassandra with single node and multi-node configuration and executed database queries using CQLSH tool along with an overview of nodetool utility to monitor and manage Cassandra cluster in Windows operating system.
