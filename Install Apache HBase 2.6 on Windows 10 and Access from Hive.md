# Install Apache HBase 2.6 on Windows 10 and Access from Hive

**Apache HBase** is the Hadoop database which is a distributed, scalable storage system for Big Data. It is an open source non-relational database modeled after Google’s Bigtable to store and fast access 
the large amount of Big Data on top of HDFS.

Google’s **Bigtable** is a distributed storage system provided by **Google File System** for managing structured data of petabytes size across thousands of commodity servers whereas **Apache HBase** is a distributed 
storage system for managing non-relational data _(structured, semi-structured and unstructured data)_ on top of Hadoop Distributed File System.

HBase can be installed in three different modes:
1. **Standalone Mode:** In Standalone Mode, HBase daemons – **HMaster**, **HRegionServer** and **ZooKeeper** – runs within a single JVM (Java Virtual Machine) process /instance on a single host with
   HBase data stored on local file system. This is the most basic deployment mode.

2. **Pseudo-Distributed Mode:** In this mode, HBase still runs completely on a single host but each HBase daemon such as **HMaster**, **HRegionServer** and **ZooKeeper** runs as a separate process.
   This mode allows us to configure HBase data storage either on the local file system or on HDFS. This deployment mode is for testing purposes only but not for Production.

3. **Fully-Distributed Mode:** In this mode, HBase cluster contains multiple nodes or hosts and each node can run one or more HBase daemons including primary and backup **Master** instances,
   multiple **RegionServer** nodes and multiple **ZooKeeper** nodes. This mode can only run on top of HDFS which makes a fully distributed configuration for Production and is used in real-time scenarios.

This document provides instructions to install **Standalone cluster** of **Apache HBase 2.6** on Windows and access it from Hive.  
You can also go through [this PDF document](/doc/Install%20Apache%20HBase%202.6%20on%20Windows%2010.pdf) for installation steps along with screenshots.  
<br/>

## 1.	Prerequisites:
The following prerequisites need to be installed before running HBase.
1. **File Archiver:** Any file archiver such as **7zip** or **WinRAR** is needed to unzip the downloaded HBase binaries.  
   7zip can be downloaded from the [official 7zip Downloads](https://www.7-zip.org/download.html) website
   and WinRAR can be downloaded from the [official RAR lab Downloads](https://www.rarlab.com/download.htm) website.

2. **JRE 8:** HBase 2.x requires Java 8 runtime environment _(HBase 2.3+ supports Java 8 mainly and Java 11 with limited capability. Other Java versions are not supported)_.
   See [HBase Java support page](https://hbase.apache.org/book.html#java) for more details.  
   We can either download just **JRE 8** (Java Runtime Environment) for Windows offline installation from the official [Java Download for Windows Offline](https://www.java.com/en/download/windows_offline.jsp) 
   website or download the whole **JDK 8** (Java Development Kit) directly from [Oracle Java Downloads](https://www.oracle.com/java/technologies/downloads/#java8) website.
   For the complete JDK installation steps, look at [here](https://github.com/srimarrivada/JDK-Installation/blob/main/Install%20JDK8%20on%20Windows.md).
<br/>

## 2. Install Standalone HBase:
Let us see how HBase can be installed in standalone mode without Hadoop requirement on Windows system.

### 2.1.	Download HBase Binaries:
Download the stable version of HBase from the official [Apache HBase Downloads](https://hbase.apache.org/downloads.html) website. At the time of this document preparation, the most recent stable release 
is 2.6.0. Select the latest version and click on **bin** link _(Check [here](https://archive.apache.org/dist/hbase/) for previous versions of HBase)_.

You will then be navigated to [Hbase 2.6.0 mirror website](https://www.apache.org/dyn/closer.lua/hbase/2.6.0/hbase-2.6.0-bin.tar.gz) where click on 
the [suggested location](https://dlcdn.apache.org/hbase/2.6.0/hbase-2.6.0-bin.tar.gz) for `hbase-2.6.0-bin.tar.gz` file that gets downloaded to your **Downloads** folder in your machine.

After the binary file is downloaded, unpack it using any file archiver (**7zip** or **WinRAR**) utility as below:

* Choose the installation directory in your machine and copy `hbase-2.6.0-bin.tar.gz` file to that directory. Here, we are choosing HBase installation directory as `D:\ProgramFiles\HBase`.

* Right click on `hbase-2.6.0-bin.tar.gz` and choose **7-Zip** -> **Extract Here** option which extracts a new packed file `hbase-2.6.0-bin.tar`.

* Next, unpack `hbase-2.6.0-bin.tar` file using **7zip** utility.

* The tar file extraction may take few minutes to finish. After finishing, you see a folder named `hbase-2.6.0` which consists of HBase binaries and libraries.

### 2.2. Download Hadoop Binaries:
Since HBase is not configured to run on Windows system by default, it is necessary to get Hadoop’s native IO utilities for Windows from [cdarlint GitHub repository](https://github.com/cdarlint/winutils). 
Note that HBase 2.6.x version supports Hadoop 3.3.5 or more. Look at [HBase Hadoop Support Matrix](https://hbase.apache.org/book.html#hadoop) to know more about the supported versions of Hadoop for HBase.

* Since we installed the HBase 2.6.0 version, download utilities such as `winutils.exe` and `hadoop.dll` of the latest Hadoop 3.3.6 version from [here](https://github.com/cdarlint/winutils/tree/master/hadoop-3.3.6/bin).

* Create a folder named `hadoop-winutils` in your D drive and create a sub-folder named `bin` inside it.

* Move `winutils.exe` and `hadoop.dll` files from your downloaded location to `D:\hadoop-winutils\bin` directory.

### 2.3. Set up Environment variables:
After installing pre-requisites and HBase binaries, we should configure the below environment variables defining Java and HBase default paths.

•	**JAVA_HOME:** This is the JDK installation directory path in the machine _(in my machine, it is `D:\ProgramFiles\Java\jdk-1.8`)_. Ignore it if this is already done.

•	**HADOOP_HOME:** This is the Hadoop’s WinUtils path in the machine _(in our case, it is `D:\hadoop-winutils`)_

•	**HBASE_HOME:** This is the HBase installation directory path in the machine _(in our case, it is `D:\ProgramFiles\HBase\hbase-2.6.0`)_

_**Note:**_  
These variables need to be added to either **User environment** variables or **System environment variables** depending on HBase configuration needed **for a single user** or **for multiple users**.  
  
In this tutorial, we will add **User environment variables** since we are configuring HBsae for a single user but if you would like to configure HBse for multiple users, then define System environment variables.  
<br/>

Follow these steps to set environment variables:
1. In the Windows search bar, start typing “environment variables” and select the first match which opens up **System Properties** dialog.

2. On the **System Properties** window, press **Environment Variables** button.

3. On the **Environment Variables** dialog:
   * Press New under **User variables** section. Add variable name `JAVA_HOME` and value `D:\ProgramFiles\Java\jdk-1.8` _(the path where your Java was installed)_. Then, press OK.
   * Press New again. Add variable name `HADOOP_HOME` and value `D:\hadoop-winutils` and press OK.
   * Press New again. Add variable name `HBASE_HOME` and value `D:\ProgramFiles\HBase\hbase-2.6.0`_(the path where your HBase was installed)_ and press OK.
   * Select **Path** variable and press **Edit** button. Press **New** and add the following values and press OK.  
     `%JAVA_HOME%\bin`  
     `%HADOOP_HOME%\bin`  
     `%HBASE_HOME%\bin`  
   * Press OK to apply environment variable changes and close window.

### 2.4. Configure HBase:
Next, we should modify the following files to configure the standalone HBase:  
`%HBASE_HOME%\conf\hbase-site.xml`  
`%HBASE_HOME%\conf\hbase-env.cmd`

#### HBase Site Configuration:
HBase needs a directory location to store the HBase and Zookeeper files which should be specified in `hbase-site.xml` file. If not specified, it automatically creates directories under `%TEMP%` directory. 

Open `hbase-site.xml` file in `HBASE_HOME\conf` folder and replace the existing properties with the below lines inside the `<configuration>` tag:
```
  <property>
    <name>hbase.cluster.distributed</name>
    <value>false</value>
  </property>
  <property>
    <name>hbase.unsafe.stream.capability.enforce</name>
    <value>false</value>
  </property>
  <property>
    <name>hbase.rootdir</name>
    <value>file:///D:/ProgramFiles/HBase/hbase-2.6.0/hbase</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/D:/ProgramFiles/HBase/hbase-2.6.0/zookeeper</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>localhost</value>
  </property>
  <property>
    <name>hbase.tmp.dir</name>
    <value>/D:/ProgramFiles/HBase/hbase-2.6.0/tmp</value>
  </property>
```

Note that HBase creates `hbase`, `zookeeper` and `logs` folders in `%HBASE_HOME%` location automatically when it is started.

#### HBase Env Configuration:
Open `hbase-env.cmd` file in `%HBASE_HOME%\conf` folder and add the following lines:
```
set JAVA_HOME=%JAVA_HOME%
set HBASE_CLASSPATH=%HBASE_HOME%\lib\client-facing-thirdparty\*
set HBASE_HEAPSIZE=8000
set HBASE_OPTS="-XX:+UseConcMarkSweepGC" "-Djava.net.preferIPv4Stack=true"
set SERVER_GC_OPTS="-verbose:gc" "-XX:+PrintGCDetails" "-XX:+PrintGCDateStamps" %HBASE_GC_OPTS%
set HBASE_USE_GC_LOGFILE=true
set HBASE_JMX_BASE="-Dcom.sun.management.jmxremote.ssl=false" "-Dcom.sun.management.jmxremote.authenticate=false"
set HBASE_MASTER_OPTS=%HBASE_JMX_BASE% "-Dcom.sun.management.jmxremote.port=10101"
set HBASE_REGIONSERVER_OPTS=%HBASE_JMX_BASE% "-Dcom.sun.management.jmxremote.port=10102"
set HBASE_THRIFT_OPTS=%HBASE_JMX_BASE% "-Dcom.sun.management.jmxremote.port=10103"
set HBASE_ZOOKEEPER_OPTS=%HBASE_JMX_BASE% -Dcom.sun.management.jmxremote.port=10104"
set HBASE_REGIONSERVERS=%HBASE_HOME%\conf\regionservers
set HBASE_LOG_DIR=%HBASE_HOME%\logs
set HBASE_IDENT_STRING=%USERNAME%
set HBASE_MANAGES_ZK=true
```

### 2.5 Verify HBase Installation:
Open **Windows PowerShell** and run the following command to verify if HBase is installed properly:
```
hbase version
```
Once the above command is executed, you will see **HBase 2.6.0** which indicates that HBase has been installed successfully.

**Note:**   
If you would like to check the hbase version using **Command Prompt**, then you must first navigate to the location where HBase is installed as below:
```
D:
cd %HBASE_HOME%\bin
hbase version
```

### 2.6 Start HBase:
Open **Windows PowerShell** as **Administrator** and start HBase using the below command:
```
start-hbase.cmd
```

**Note:**   
If you would like to start hbase using **Command Prompt**, then you must first navigate to the location where HBase is installed as below:
```
D:
cd %HBASE_HOME%\bin
start-hbase.cmd
```
Once the above command is executed, it opens up a new command prompt where you can see three daemon services **Zookeeper**, **HMaster** and **HRegion** server are started. 
Wait until the **HMaster initialization is complete**.

To verify the HBase has been started successfully, use the below command:
```
jps
```
It should display `HMaster` process which denotes HMaster, single HRegionServer and Zookeeper services are running in a single JVM.

### 2.7 HBase Shell:
Open **Windows PowerShell** and run the below command to launch HBase shell:
```
hbase shell
```
If you would like to launch hbase from **Command Prompt**, then you must first navigate to the location where HBase is installed as below:
```
D:
cd %HBASE_HOME%\bin
hbase shell
```
While launching the hbase shell, you may encounter an error **"This file has been superceded by packaging our ruby files into a jar and using jruby's bootstrapping to invoke them."**

To resolve this error, follow the below steps:
* Edit `hbase.cmd` file in `%HBASE_HOME%\bin` location and replace the following lines of code under `:shell` label.

  **Before:**
  ```
  :shell
    rem find the hbase ruby sources
    if exist "%HBASE_HOME%\lib\ruby" (
      set HBASE_OPTS=%HBASE_OPTS% -Dhbase.ruby.sources="%HBASE_HOME%\lib\ruby"
    ) else (
      set HBASE_OPTS=%HBASE_OPTS% -Dhbase.ruby.sources="%HBASE_HOME%\hbase-shell\src\main\ruby"
    )
    set HBASE_OPTS=%HBASE_OPTS% %HBASE_SHELL_OPTS%
  
    set CLASS=org.jruby.Main -X+O %JRUBY_OPTS% "%HBASE_HOME%\bin\hirb.rb"
    goto :eof
  ```
  **After:**
  ```
  :shell
    rem find the hbase ruby sources
    if exist "%HBASE_HOME%\lib\ruby" (
      @rem set HBASE_OPTS=%HBASE_OPTS% -Dhbase.ruby.sources="%HBASE_HOME%\lib\ruby"
	    set JRUBY_OPTS="${JRUBY_OPTS} -X+O"
	    set CLASS="org.jruby.JarBootstrapMain"
    ) else (
      set HBASE_OPTS=%HBASE_OPTS% -Dhbase.ruby.sources="%HBASE_HOME%\hbase-shell\src\main\ruby"
	    set CLASS="org.jruby.Main -X+O %JRUBY_OPTS% %HBASE_HOME%/hbase-shell/src/main/ruby/jar-bootstrap.rb"
    )
    set HBASE_OPTS=%HBASE_OPTS% %HBASE_SHELL_OPTS%
    
    @rem set CLASS=org.jruby.Main -X+O %JRUBY_OPTS% "%HBASE_HOME%\bin\hirb.rb"
    goto :eof
  ```

* Then download `jansi-1.18.jar` file from [Maven repository](https://mvnrepository.com/artifact/org.fusesource.jansi/jansi/1.18) and place the file under `%HBASE_HOME%\lib` location.

* Now, you should be able to launch the hbase shell 
  ```
  hbase shell
  ```
  Use `quit` or `exit` command  to come out of HBase interactive shell.

**<ins> Note: </ins>** 
**HBase 2.x version does not support installing Pseudo Distributed mode of HBase in Windows Operating System.**
<br/>
<br/>

## 3.	HBase Shell Commands:
HBase provides an interactive tool called `hbase shell` where you can execute commands to interact with HBase server. 

To interact with the database, HBase Shell commands are divided into 14 groups: 
1.	General Commands
2.	Namespace Commands
3.	Data Definition Commands
4.	Data Manipulation Commands
5.	Tool Commands
6.	Cluster Replication Commands
7.	Snapshot Commands
8.	Configuration Commands
9.	Quota Commands
10.	Security Commands
11.	Procedure Commands
12.	Visibility Label Commands
13.	RS Group Commands
14.	Store File Tracker Commands

Once you entered into HBase shell, just run `help` command to get the list of all shell commands. 

To know the usage of each shell command, run `help '<command>'` (i.e after `help`, give a space and enter the command name in single quotes).

For example, run the following to get more help of `status` command:
```
help 'status'
```

### HBase Status:
On the `hbase>` prompt, run the following command to get the status of HBase cluster:
```
status
```
After the above command is executed, it dispalys that there is **1 active master** and **0 backup masters** available out of which **1 server is active** and **0 servers are dead**. 
The **average load value** is 2.00.

### HBase Version:
Run the following command to get the installed version of HBase:
```
version
```
After the above command is executed, you will see that HBase version as **2.6.0**.

### Process List:
Run the following command to get the list of process running or completed in HBase:
```
processlist
```

### List Namespace:
On `hbase>` prompt, use `list_namespace` command to get the list of existing namespaces available on the HBase database as below:
```
list_namespace
```

### Create Namespace:
Create a namespace using `create_namespace` command in HBase database. It takes a namespace name as mandatory argument. 

Run the following command to create a namespace called `test` in our HBase:
```
create_namespace 'test'
```

### Describe Namespace:
Get the existing namespace details using `describe_namespace` command.

Run the following command to get the properties of `test` namespace:
```
describe_namespace 'test'
```

### Create Table:
Use `create` command to create a HBase table. It takes a table name and column family as mandatory arguments. The syntax to create a table is as follows:
```
create 'namespace:table_name', 'column_family1', 'column_family2'
```
or
```
create 'namespace:table_name', {NAME => 'column_family1'}, {NAME => 'column_family2'}
```

Note that namespace is optional here and when it is not specified, the table name is created in the default namespace. 
You can specify the column family as either a simple string or in the form of dictionaries using `NAME` attribute.

Run the following command to create an `employee` table with `emp` column family and `office` column family in `test` namespace:
```
create 'test:employee', 'emp', 'office'
```

### List Table:
Use `list` command to show all user tables in HBase
```
list
```

You can also filter the output using regular expression in the `list` command as below:
```
list '.*emp.*'
```
The above command displays the list of table names containing `emp` string.

### Describe Table:
Use `describe` command to describe details such as version, compression, blocksize, replication, etc. of HBase table.

Run the following command to get the details of employee table available in `test` namespace:
```
describe 'test:employee'
```

### Insert Data into Table:
Use `put` command to insert data to rows and columns in a HBase table. This is similar to `INSERT` statement in RDBMS but the syntax is different as follows:
```
put 'namespace:table_name', 'row_key', 'column_family:column_name', 'value'
```

Run the following commands to create 3 employee records using their employee IDs as row key and into columns such as `name`, `gender` under `emp` column family 
and `address`, `city`, `state`, `country` under `office` column family:
```
put 'test:employee', '101', 'emp:name', 'Scott'
put 'test:employee', '101', 'emp:gender', 'M'
put 'test:employee', '102', 'emp:name', 'Mark'
put 'test:employee', '103', 'emp:name', 'Linda'
put 'test:employee', '103', 'emp:gender', 'F'
put 'test:employee', '102', 'office:address', '101 BayHill Drive'
put 'test:employee', '102', 'office:city', 'Bentonville'
put 'test:employee', '102', 'office:state', 'Arkansas'
put 'test:employee', '102', 'office:country', 'USA'
put 'test:employee', '103', 'office:city', 'Tampa'
put 'test:employee', '103', 'office:state', 'Florida'
put 'test:employee', '103', 'office:country', 'USA'
put 'test:employee', '103', 'office:city', 'Jacksonville'
```

Note that the last command inserts a new value `Jacksonville` at `city` column in `office` column family for the `103` row key. 
When you put a new value at the same column for the same row key, HBase does not do any update but it assigns the same column with a new timestamp so that when you retrieve the data, 
it fetches the latest data from columns.

### Read Data from Table:
We can use `get` and `scan` commands to retrieve data from HBase table depending on whether to retrieve single row or multiple rows of data.

* Use `get` command to get single row data and its columns from a HBase table. 
   
  For example, run the following command to get the row key `102` from `employee` table:
  ```
  get 'test:employee', '102'
  ```
  
  Run the following command to get the row key `103` with `emp:name` and `emp:gender` columns only from `employee` table:
  ```
  get 'test:employee', '103', {COLUMNS => ['emp:name','emp:gender']}
  ```
  
* Use `scan` command to fetch multiple rows of data from a HBase table. By default, it fetches all data from the table.
  
  Run the following command to fetch all records from `employee` table available in `test` namespace:
  ```
  scan 'test:employee'
  ```
  
  The `scan` command can take additional parameters in dictionary format to retrieve specific columns with specific rows of data.
  
  Run the below command to retrieve the first 2 rows with `emp:name` and `office:country` columns:
  ```
  scan 'test:employee',{COLUMNS => ['emp:name','office:country'], LIMIT => 2}
  ```
  
  Run the below command to retrieve `emp:name` and `emp:gender` columns from starting row key `102` and ending row key `103`:
  ```
  scan 'test:employee',{COLUMNS => ['emp:name','emp:gender'], STARTROW => '102', STOPROW => '103'}
  ```

### Disable Table:
Use `disable` command to disable a table which is needed before deleting a table or changing its setting in HBase.

Run the following command to disable employee table available in `test` namespace:
```
disable 'test:employee'
```

Use `is_disabled` command as below to check the disabled status of a table:
```
is_disabled 'test:employee'
```
It displays the status as `true` which means that our table is disabled already

When you `describe` the disabled table, it shows the table status as disabled:
```
describe 'test:employee'
```

Note that when you try to fetch data from the disabled table, it throws an error.

### Enable Table:
Use `enable` command to enable a table which is needed to perform any regular commands

Run the following command to enable `employee` table available in `test` namespace:
```
enable 'test:employee'
```
Use `is_enabled` command as below to check the enabled status of a table:
```
is_enabled 'test:employee'
```
It displays the status as `true` which means that our table is enabled already.

When you `describe` the enabled table, it shows the table status as enabled:
```
describe 'test:employee'
```

### Delete Rows from Table:
HBase provides `delete` and `deleteall` commands to delete a single cell or multiple cells at a given row in a table.

* Use `delete` command to delete a cell at specified row in a table.  
  For example, run the following command to remove `office:state` column at `102` row key from `employee` table:
  ```
  delete 'test:employee', '102', 'office:state'
  ```
  
  When you `scan` the employee table, you will notice that `office:state` column has been removed at `102` row key only while this column data is present for other rows.
  ```
  scan 'test:employee'
  ```
  
* Use `deleteall` command to delete all cells at specified row in a table.  
  For example, run the following command to remove all columns data at `103` row key from `employee` table:
  ```
  deleteall 'test:employee', '103'
  ```
  
  When you `scan` the `employee` table, you will notice that `103` row key has been removed completely.
  ```
  scan 'test:employee'
  ```

### Drop Table:
Use `drop` command to delete a table from HBase database. Note that you must disable the table before dropping it.

Run the following commands to disable `employee` table and then drop it in `test` namespace:
```
disable 'test:employee'
drop 'test:employee'
```

HBase also provides `drop_all` command to delete multiple tables using a regular expression as below:
```
drop_all '.*emp.*'
```

## 4. HBase User Interface:
Each HBase service provides a simple Web UI, which gives us the basic information about the current cluster state, change log level, access logs and many more. 
These Web UIs are useful for troubleshooting and diagnostics. The Web UIs are embedded using the Jetty Web Server _(which has a low memory footprint and fast response times)_, 
so they start when we run the relevant service.

### 4.1. Master Web UI:
The Master server web UI helps to check the overall health of HBase system and displays the currently running Region servers, backup masters and HBase tables. 
By default, the Web UI runs on **16010** port and is accessible at http://localhost:16010/.  If multiple masters are running, only the active master shows all information.

The Master web UI shows the created tables and their definition _(such as ColumnFamilies, blocksize, etc.)_. It also lists the summary of available RegionServers in the cluster stating how many regions 
each RegionServer is serving, how many requests per second they are handling, usedHeap, maxheap, etc. 

### 4.2. Region Web UI:
HBase also provides a web UI for Region Server runs on **16030** port by default and is accessible at http://localhost:16030/. This web interface provides the detailed information about the performance of 
each Region server and data it is providing.

The **Server Metrics** section tells us how each Region server is working by showing how many regions it is hosting and how many requests per second it is serving.  
The **Tasks** sections displays the long-running tasks and their status.
<br/>
<br/>

## 5. Access HBase from Hive:
Since we are more comfortable on running SQL like queries on any database, we can leverage **Apache Hive** to access HBase and get the benefit of SQL syntax and run queries on HBase tables. 
To do so, we must use `HBaseStorageHandler` java class from `hive-hbase-handler.jar` file to register HBase tables with the Hive metastore.  

First, make sure that **Apache Hive** is installed in your machine. If not already installed, follow 
[this guide](https://github.com/srimarrivada/BigData-Installation-on-Windows/blob/main/Install%20Apache%20Hive%204.0%20on%20Windows%2010%20and%20Configure%203%20Metastore%20Modes.md) and install it. 

### 5.1. Verify Hadoop Installation:
Open **Windows PowerShell** or **Command Prompt** and run the below command to verify the Hadoop version:
```
hadoop version
```
It displays the installed version of Hadoop _(in my case, Hadoop 3.3.6 version was installed)_.

### 5.2. Verify Hive Installation:
Open **Windows PowerShell** or **Command Prompt** and run the below command to verify the Hive version:
```
hive --version
```
This may take couple of minutes to complete and displays the installed version of Hive _(in my case, Hive 3.1.3 version was installed)_.

### 5.3. Verify Hive HBase Handler:
Go to `%HIVE_HOME%\lib` directory and verify `hive-hbase-handler-x.x.x.jar` file is available. 

If you can’t find `hive-hbase-handler-x.x.x.jar` file in your Hive installation path, you can download it for the corresponding Hive version from 
[Maven Repository](https://mvnrepository.com/artifact/org.apache.hive/hive-hbase-handler) and place it in `%HIVE_HOME%\lib` location.

### 5.4. Start Hadoop Services:
Before starting Hive, Hadoop services must be running since Hive runs on top of HDFS.

Open **Windows Command Prompt** or **Windows PowerShell** in **Administrator** mode and run the following commands to start Hadoop services:
```
D:
cd %HADOOP_HOME%\sbin
start-dfs.cmd
start-yarn.cmd
```
 
After executing the above commands, you will see four Windows command prompts opened for `namenode`, `datanode`, `resourcemanager` and `nodemanager`.  

Wait until `namenode` service says **Quota initialization completed** and `datanode` service says **Successfully sent block report to namenode: localhost/127.0.0.1:9820**.  
Wait until `resourcemanager` service says **Transitioned to active state** and `nodemanager` service says **Registered with ResourceManager** to ensure all services are started properly.

### 5.5. Start Hive Metastore Service:
It is necessary to start Hive metastore service if your Hive metastore is configured in Remote database _(If you Hive metastore is configured locally, then skip this step)_.

Verify your Hive metastore configuration in `%HIVE_HOME%\conf\hive-site.xml` file in which you can look for `javax.jdo.option.ConnectionURL` property which tells the metastore connectivity 
_(In my case, Hive metastore is connected to MySQL running locally)_.

Since Hive can connect to remote metastore using Thrift URIs, start the `metastore` service using the below command:
```
hive --service metastore --hiveconf hive.root.logger=console
```
After the above command is executed, you will notice that metaserver started on port **9083** and connected to underlying **MySQL** database.

### 5.6. Launch Hive Shell:
Now, open **Command Prompt** or **Windows PowerShell** and run the following command to launch Hive shell:
```
hive
```
Here, you might face an error **java.lang.NoClassDefFoundError: org/apache/htrace/core/Tracer$Builder**. This issue occurs when `htrace-core.jar` file is missing in `%HBASE_HOME%\lib` directory. 

To resolve the above error, go to `%HBASE_HOME%\lib\client-facing-thirdparty` location and copy `htrace-core4-x.x.x-incubating.jar` file to `%HBASE_HOME%\lib` directory.

Additionally, I would suggest you to set the following properties which are helpful to disable unnecessary logging on the Hive console:
* Set the environment variable `HADOOP_USER_CLASSPATH_FIRST` to `true`.
* Open `hive-config.cmd` file in `%HIVE_HOME%\bin` directory and add the below line at the end of the file:
  ```
  set HADOOP_CLIENT_OPTS=%HADOOP_CLIENT_OPTS% -Dlog4j.configurationFile=hive-log4j2.properties
  ```
Now, you should be able to launch the hive shell successfully.

### 5.7. Create HBase Table:
Use the following syntax to create a new HBase table managed by Hive using the `STORED BY` clause pointed to `HBaseStorageHandler` class:
```
CREATE TABLE hive_table_name (key datatype, hive_column datatype) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key, hbase_column_family:hbase_column") 
TBLPROPERTIES ("hbase.table.name" = "hbase_table_name");
```
Here, the `hbase.columns.mapping` property is required and is used to map the column names between HBase and Hive tables. The `hbase.table.name` property is optional and it controls the name of the table 
as known by HBase, and allows the Hive table to have a different name. If not specified, then the Hive and HBase table names will be identical.

On the Hive shell, run the following command to create `hbase_departments` table in Hive with HBase table name as `departments`:
```
CREATE TABLE hbase_departments (dept_id int, name string, manager string, city string, state string, country string, pincode int)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key, dept:name, dept:manager, location:city, location:state, location:country, location:pincode")
TBLPROPERTIES ("hbase.table.name" = "departments");
```

Describe the newly created table with this hive command:
```
describe formatted hbase_departments;
```

### 5.8. Insert Data into HBase Table:
Run the following command to insert record into `hbase_departments` table:
```
INSERT INTO hbase_departments VALUES (10, 'Finance', 'Steve', 'Houston', 'Texas', 'USA', 633001);
```
This Insert statement triggers a MapReduce job that will get executed on the Hadoop cluster.  
Here, you might get an **Execution error** and the details of the error can be seen on the **Hadoop YARN UI**. 

* Open YARN UI at http://localhost:8088/cluster and click on the Application ID value.

* Click on **Logs** link available for one of the attempts.

* Select **syslog** link.

* You can see a detailed error message as below:  
  _java.lang.ClassCastException: org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$GetFileInfoRequestProto cannot be cast to com.google.protobuf.Message_

The above `ClassCastException` occurs when HBase is referring to different version of Hadoop libraries compared to libraries available on the existing Hadoop cluster. 
To check this, go to `%HBASE_HOME%\lib` location where you can see all hadoop jars are of **2.10.2** version while our Hadoop is actually running on **3.3.6** version.

Follow the below steps to resolve `ClassCastException` issue:
* Close hive terminal window, Hive metastore service window and `hbase-master start` window.

* Remove or rename all `hadoop*.jar` files in `%HBASE_HOME%\lib` location _(I renamed all `haddop*.jar` files instead of deleting them)_.

* Open **Windows Powershell** as **Administrator** and run the following command to start HBase cluster:
  ```
  start-hbase.cmd
  ```

* Open a new **Command Prompt** and start the metastore service using the below command:
  ```
  hive --service metastore --hiveconf hive.root.logger=console
  ``

* Open a new **Command Prompt**, launch Hive shell and run the following insert statement:
  ```
  INSERT INTO hbase_departments VALUES (10, 'Finance', 'Steve', 'Houston', 'Texas', 'USA', 633001);
  ```
  It submitted a new application to the Hadoop YARN and provided an application URL which is successful and inserted record into table.
  
  You can notice the same on the YARN UI at http://localhost:8088/cluster where the latest application status is FINISHED successfully.

Let us quickly verify the data in the table by running the following select statement:
```
select * from hbase_departments;
```

To view the column names in the above result, set `hive.cli.print.header property` to `true` and run the select statement.
```
set hive.cli.print.header=true;
select * from hbase_departments;
```

### 5.9. Validate Newly Created Table in HBase:
Open new **Command Prompt** and start hbase shell.
```
D:
cd %HBASE_HOME%\bin
hbase shell
```
On `hbase>` prompt, list the available tables
```
list
```
You can see that `departments` table is available which got created from Hive.

Describe the `departments` table:
```
describe "departments"
```

Scan the `departments` table to see its contents:
```
scan "departments"
```
You can see that one record of row key `10` is available in `departments` table.

### 5.10. Create External HBase Table:
If you want to give Hive access to an existing HBase table, then use `CREATE EXTERAL TABLE` command on Hive. The syntax is similar to `CREATE TABLE` command:
```
CREATE EXTERNAL TABLE hive_table_name (key datatype, hive_column datatype) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key, hbase_column_family:hbase_column") 
TBLPROPERTIES ("hbase.table.name" = "hbase_table_name");
```

Go to hive terminal and run the following command to create `hbase_external_employees` table in Hive:
```
CREATE EXTERNAL TABLE hbase_external_employees (emp_id int, name string, gender string, address string, city string, state string, country string)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key, emp:name, emp:gender, office:address, office:city, office:state, office:country")
TBLPROPERTIES ("hbase.table.name" = "test:employee");
``` 

### 5.11.	Fetch HBase Data in Hive:
Now, let us run fetch records from `hbase_external_employees` table:
```
select * from hbase_external_employees;
```
It displays 3 records which are available in the existing HBase `test:employee` table.

Similarly, we can run SQL queries on Hive which pulls data from HBase.
```
select count(emp_id) from hbase_external_employees;
```

The above command submits a MapReduce job to YARN and up on successful, it displays the total of records in the table.
<br/>
<br/>

## 6.	Shutdown Standalone HBase:
We can shutdown the standalone Hbase cluster in the Windows environment.

Open **Windows PowerShell** as **Administrator** and shutdown the entire HBase cluster using the below command:
```
hbase master stop -shutDownCluster
```
**Note:**  
If you would like to stop hbase using **Command Prompt**, then you must first navigate to the location where HBase is installed as below.
```
D:
cd %HBASE_HOME%\bin
hbase master stop -shutDownCluster
```
This will shut down three daemon services **HRegion**, **HMaster** and **Zookeeper** _(you will see the same in the another Command Prompt where HBase Distribution was running)_
<br/>
<br/>

**Congratulations!! You have successfully installed the standalone HBase 2.6 in Windows operating system and executed few HBase commands. 
You also understood how to access HBase tables from Hive and run SQL type queries on HBase.**
