# Install Apache Hive 3.1.3 on Windows 10 and Configure 3 Metastore Modes

**Apache Hive** was developed by Facebook and became an open-source ETL and data warehousing tool which is built on top of Hadoop for analyzing, querying and managing large datasets stored in HDFS. 
Hive uses **HQL** (Hive Query Language) as a processing engine that processes HDFS datasets such that queries executed from Hive are internally converted into MapReduce tasks for parallel computation 
and distribution of data. 

The key components of Apache Hive include **Hive CLI**, **Beeline CLI**, **HiveServer2**, **Hive Web Interface**, **Hive Driver**, **Hive Metastore**, **HCatalog** and **WebHCat**.

**Hive Metastore** is a critical component of Hive because it is the central schema repository that stores Hive metadata including tables, columns, datatypes, data locations etc. created by Hive 
and this schema repository can be used by other data processing tools such as Spark, Pig etc.

**Hive Metastore** works in three different modes:
1.	**Embedded Metastore:** In this mode, Hive Metastore service runs in the same JVM where Hive Driver service runs and it uses Apache Derby as metastore database that is stored on the local file system.
This is the default metastore that comes with Hive installation and is used for testing purposes only. Only one embedded Derby database can access database files at any time so only one Hive session
can be opened and if we try to start the second Hive session, it errors out. To allow multiple Hive sessions, we can configure Derby to run as Network Server.

2.	**Local Metastore:** In this mode, Hive Metastore and Hive Driver still run within the same JVM process but metastore service connects to a JDBC supported database such as Derby, MySQL etc that runs
on a different JVM in the same machine or on different machine. Local metastore currently supports **Derby**, **MySQL**, **MSSQL**, **Oracle** and **Postgres**  database systems only.

3.	**Remote Metastore:** In this mode, Hive Metastore service runs in a different JVM but not in Hive Driver service JVM and metastore service connects to a remote database which could be **MySQL**, **MSSQL**,
**Oracle** or **Postgres**. In Remote Metastore, Hive Client makes a connection to Hive Metastore using Thrift protocol, and Metastore server in turn communicates with the database and run queries.

This document provides instructions to install **Hive 3.1.3** version on top of **Hadoop 3.x** and configure Hive metastore in all 3 modes.
You can also go through [this PDF document](/doc/Install%20Apache%20Hive%203.1.3%20on%20Windows%2010.pdf) for installation steps along with screenshots.  
<br/>

## 1. Prerequisites
The following prerequisites need to be installed before running Hive.
1.	**Hadoop:** Before installing Hive, Hadoop cluster must have been installed and running. 
Go through [these steps](https://github.com/srimarrivada/BigData-Installation-on-Windows/blob/main/Install%20Apache%20Hadoop%203.3.6%20on%20Windows%2010.md) to install Hadoop on Windows operating system.

2.	**File Archiver:** Any file archiver such as **7zip** or **WinRAR** is needed to unzip the downloaded Hive binaries.  
    7zip can be downloaded from the [official 7zip Downloads](https://www.7-zip.org/download.html) website
  	and WinRAR can be downloaded from the [official RAR lab Downloads](https://www.rarlab.com/download.htm) website.

3.	**Cygwin:** Since some Hive utilities are not compatible with Windows, we will need the Cygwin tool to run Linux commands.
    You can go through [these steps](https://github.com/srimarrivada/CygwinInstallation/blob/main/Use%20Cygwin%20to%20Run%20Linux%20Commands%20on%20Windows.md) to install Cygwin.
<br/>

## 2. Download Hive Binaries
After installing prerequisites, download  **Hive 3.1.3** release from the [Apache Hive Downloads](https://www.apache.org/dyn/closer.cgi/hive/) mirror website.
1. Go to the [suggested location](https://dlcdn.apache.org/hive/) for download and click on [hive-3.1.3/](https://dlcdn.apache.org/hive/hive-3.1.3/) from where you need to download the binary file
   named `apache-hive-3.1.3-bin.tar.gz` which gets downloaded to your **Downloads** folder.

2. After the binary file is downloaded, unpack it using any file archiver (**7zip** or **WinRAR**) utility as below:

   * Choose the installation directory in your machine and copy `apache-hive-3.1.3-bin.tar.gz` file to that directory.  
     Here, we are choosing Hive installation directory as `D:\ProgramFiles\Hive`.

   * Right click on the file and choose 7-Zip -> Extract Here option which extracts a new packed file `apache-hive-3.1.3-bin.tar`.

   * Next, unpack `apache-hive-3.1.3-bin.tar` file using 7zip utility.

   * The tar file extraction may take few minutes to finish. After finishing, you see a folder named `apache-hive-3.1.3-bin` which consists of Hive binaries and libraries.

_**Note:**_  
By default, Apache Hive is built to run on Linux Operating system. To make it running on Windows OS, we should use **Cygwin** utility to execute Linux commands from Windows. 
However, we can run some Hive utilities directly on Windows without Cygwin by downloading `.cmd` files from [HadiFadl GitHub repository](https://github.com/HadiFadl/Hive-cmd). 
These `.cmd` files are sufficient to start Hive from Windows command line but you will not be able to run some utilities such as `schematool`, `metastore`, etc. directly in which case Cygwin is required.  

Alternatively, I would suggest you to take `.cmd` files from [my GitHub repository](https://github.com/srimarrivada/Hive-Windows-Files) for the corresponding Hive version. 
For this installation, download files from [hive-3.1.3](https://github.com/srimarrivada/Hive-Windows-Files/tree/main/hive-3.1.3) location and move to the exact folder structure where ever Hive is installed in the machine. 
This makes you to start Hive as well as run other utilities including `schematool`, `metastore`, `metatool`, etc. from Windows itself. 
<br/>
<br/>

## 3. Set up Environment Variables
After installing Hadoop prerequisite and Hive binaries, we should configure two environment variables defining Hive installation path.

* `HIVE_HOME`: This is the Hive installation directory path in the machine _(in our case, it is `D:\ProgramFiles\Hive\apache-hive-3.1.3-bin`)_
* `HADOOP_USER_CLASSPATH_FIRST`: Set this variable value to `true` for Hive to use Hadoop user Class path first. This ensures **log4j2.x** and **jline** jars are loaded ahead of the jars pulled by Hadoop.

_**Note:**_  
These variables need to be added to either **User environment** variables or **System environment variables** depending on Hive configuration needed **for a single user** or **for multiple users**.  
  
In this tutorial, we will add **User environment variables** since we are configuring Hive for a single user but if you would like to configure Hive for multiple users, then define System environment variables.  
<br/>

Follow these steps to set environment variables:
1. In the Windows search bar, start typing “environment variables” and select the first match which opens up **System Properties** dialog.

2. On the **System Properties** window, press **Environment Variables** button.

3. On the **Environment Variables** dialog:
   * Press New under **User variables** section. Add variable name `HIVE_HOME` and value `D:\ProgramFiles\Hive\apache-hive-3.1.3-bin` _(the path where your Hive was installed)_. Then, press OK.
   * Press New again. Add variable name `HADOOP_USER_CLASSPATH_FIRST` and value `true` and press OK.
   * Select **Path** variable and press Edit button. Press New and add `%HIVE_HOME%\bin` value and press OK.
   * Press OK to apply environment variable changes and close window.
<br/>

## 4. Verify Hive Installation
Open **Windows PowerShell** or **Command Prompt** and run the following command to verify if Hive is insalled properly:
```
hive --version
```

This may take couple of minutes to complete and displays **Hive 3.1.3** version installed.  
<br/>

## 5. Start Hadoop Services
Before starting Hive, Hadoop services must be running since Hive runs on top of HDFS.  

Open **Windows Command Prompt** or **Windows PowerShell** in **Administrator mode** and run the following commands to start Hadoop services.
```
start-dfs.cmd
start-yarn.cmd
```

After executing the above commands, we can see four Windows command prompts opened for `namenode`, `datanode`, `resourcemanager` and `nodemanager`.
* `namenode` service displays message **“Quota initialization completed”** which means it has been started successfully.
* `datanode` service dispalys message **“Successfully sent block report to namenode: localhost/127.0.0.1:9820”** which means it has been started successfully.
* `resourcemanager` service dispalys message **“Transitioned to active state”** which means it has been started successfully.
* `nodemanager` service displays message **“Registered with ResourceManager”** which means it has been started successfully.

You can also verify if these services are running by using `jps` command that you can execute in Command Prompt.  
<br/>

## 6. Configure Embedded Derby Metastore
Hive by default runs with Embedded Metastore of Derby database on Hadoop File System.

### Initialize Hive Metastore:
To start Hive, we need to initialize the Hive Metastore (`metastore_db`) using `schematool` utility provided by Hive.

Execute this command in Command Prompt or Windows PowerShell:
```
schematool -dbType derby -initSchema
```

After executing the above command, it creates `metastore_db` folder, `beeline` folder and `derby.log` file in the location from where ever `schematool` utility got executed.
* `metastore_db` folder contains the database files of Hive metastore.
* Open `derby.log` file to see that Hive has booted **Apache Derby database of 10.14.1.0** version.  

### Start Hive CLI:
Now, we can start hive command line interface using the below command:
```
hive
```

To see the detailed logging of hive CLI, use the following command for starting hive:
```
hive --hiveconf hive.root.logger=console
```

As soon as Hive is started, it creates a `/tmp/hive` folder on Hadoop File System.  

Open another command prompt and execute this command to see `/tmp/hive` folder on HDFS:
```
hadoop fs -ls /tmp
```
Hive uses `/tmp` HDFS directory for storing `temp_space` database which is available at `/tmp/hive/<userid>/<temp_folder>/_temp_space.db` 

### Run Queries on Hive CLI:
Open Hive CLI and run the following queries to create a hive metadata database, create table, load data and select data.

* **Create a database in Hive metastore:**
  ```
  create database hive_embedded_derby_db;
  show databases;
  ```
  As soon as the above query is executed, Hive creates the default warehouse directory `/user/hive/warehouse` in which `hive_embedded_derby_db.db` folder is created 
  in Hadoop File System that you can validate with this command:
  ```
  hadoop fs -ls /user/hive/warehouse
  ```

  We can also verify this in NameNode UI: http://localhost:9870/dfshealth.html

  Open **NameNode UI**, go to **Utilities** tab and select **Browse the file system** option. Enter the directory name `/user/hive/warehouse` and you can see `hive_embedded_derby_db.db` folder available.  
  <br/>
  
* **Create a table in Hive metastore:**  
  ```
  use hive_embedded_derby_db;
  create table employees(emp_id int, emp_name string, emp_salary int);
  show tables;
  ```
  The above table is saved under `/user/hive/warehouse/hive_embedded_derby_db.db` HDFS location that you can validate with this command:
  ```
  hadoop fs -ls /user/hive/warehouse/hive_embedded_derby_db.db
  ```
  We can verify the same in **NameNode UI**. Click on `hive_embedded_derby_db.db` folder in `/user/hive/warehouse` directory and you can see `employees` folder available.  
  <br/>
   
* **Insert data into the table:**  
  ```
  insert into employees values (101, 'johnson',5000);
  ```
  The above insert command submits the MapReduce job to get the record into table.

  We can track the job status on YARN UI http://localhost:8088/cluster where you can see an application name with `insert into employees…` that was executed.
  Click on the application ID to see the additional job details and logs.

  After the above job is succeeded, a file is created under `/user/hive/warehouse/hive_embedded_derby_db.db/employees` HDFS location that you can validate with this command:
  ```
  hadoop fs -ls /user/hive/warehouse/hive_embedded_derby_db.db/employees
  ```

  In **NameNode UI**, click on `employees` folder in `/user/hive/warehouse/hive_embedded_derby_db.db` directory and you can see a file `000000_0 `available.  
  <br/>
  
* **Select data from the Hive table:**
  ```
  select * from employees;
  ```
  
  Run this command in another command prompt to validate in HDFS:
  ```
  hadoop fs -cat /user/hive/warehouse/hive_embedded_derby_db.db/employees/000000_0
  ```

  In **NameNode UI**, click on `000000_0` file and select **Head the file** or **Tail the file** to see the file contents. We can download this file by clicking on **Download** option.
  <br/>
  
_**Note:**_  
In embedded Hive metastore mode, we cannot start a second Hive session while the current one is active because embedded metastore allows only one connection at any time.

To test this, open another Command Prompt and start Hive shell and try to run a sample query (For example, `show databases;`):
```
hive
show databases;
```

You can see an error **`Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient`** because another Hive session is still active. 
Until the previous Hive session is closed, we cannot start another Hive session in Embedded metastore. 

Close or exit out of all Hive CLI and make sure that no sessions are active.  

### Beeline CLI:
**Beeline** is the advanced version of Command Line Interface that comes with **HiveServer2** service. 
Beeline is a JDBC client that is based on SQLLine CLI. In embedded mode, Beeline connects to an embedded HiveServer2 service similar to Hive CLI and run Hive commands.

**Start Beeline CLI:**  
Execute this command on Command Prompt or Powershell to start Beeline CLI:
```
beeline
```
It prints the **Beeline version 3.1.3 by Apache Hive** and provides us `beeline>` prompt.  

_**Note:**_  
To get a list of Beeline commands, type `!` on `beeline>` prompt and press `Tab` key.
Go through [this Apache Hive documentation](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) to get more understanding on these commands.  
<br/>

On `beeline>` prompt, run this command to connect to embedded Hiveserver2. When it asks for username and password, enter the default username **scott** and password **tiger** which are provided by HiveServer2.
```
!connect jdbc:hive2://
```  
<br/>

HiveServer2 credentials can also be provided in the `connect` command itself with this beeline command:
```
!connect jdbc:hive2:// -n scott -p tiger
```
or
```
!connect jdbc:hive2:// scott tiger
```  
</br>

**Start Beeline connecting to HiveServer2 directly:**  
Execute this command on Command Prompt or Powershell to start beeline with making connection to HiveServer2:
```
beeline -u jdbc:hive2:// -n scott -p tiger
```
or
```
beeline -u jdbc:hive2:// scott tiger
```  

After beeline is connected, we can execute queries as we do in Hive CLI.  
```
show databases;
use hive_embedded_derby_db;
show tables;
select * from employees;
```
To come out of beeline shell, use `!quit` command.  
<br/>

## 7. Configure Local Derby Metastore
We can configure Hive metastore in local mode with Derby database by making Derby to run in network mode. To do this, first download and install Apache Derby.

### Install Apache Derby:
It is recommended to install Derby database of version that is shown for Embedded Derby installed with Hive. 
Open `derby.log` file available in the location where `metastore_db` is created to see the embedded Derby database version.

Additionally, we can check the appropriate Derby version in [Apache Derby Downloads](https://db.apache.org/derby/derby_downloads.html) page for supported versions of Java releases. 
Since we have Java 8 to run Hadoop and Hive, the latest Derby release **for Java 8** is **Apache Derby 10.14.2.0** version.

1. Download `db-derby-10.14.2.0-bin.tar.gz` file from the [Apache Derby 10.14.2.0 Release](https://db.apache.org/derby/releases/release-10_14_2_0.html) website which gets downloaded to your **Downloads** folder.

2. After the file is downloaded, unpack it using any file archiver (**7zip** or **WinRAR**) utility as below.
   * Choose the installation directory in your machine and copy `db-derby-10.14.2.0-bin.tar.gz` file to that directory.
     Here, we are choosing Derby installation directory as `D:\ProgramFiles\Derby`.
   
   * Right click on `db-derby-10.14.2.0-bin.tar.gz` and choose 7-Zip -> Extract Here option which extracts a new packed file `db-derby-10.14.2.0-bin.tar`.
   
   * Next, unpack `db-derby-10.14.2.0-bin.tar` file using 7zip utility.
   
   * The tar file extraction may take few minutes to finish. After finishing, you see a folder named `db-derby-10.14.2.0-bin` which consists of Derby binaries and libraries.

### Set up Environment Variables:
After installing Derby, we should configure two environment variables defining Derby installation path.

* `DERBY_HOME`: This is the Derby installation directory path in the machine _(in our case, it is `D:\ProgramFiles\Derby\db-derby-10.14.2.0-bin`)_
* `DERBY_OPTS`: Set it to `-Dderby.system.home=%DERBY_HOME%`. This variable is optional but required if you want to create Hive metastore database in a custom location other than default location.  
<br/>

Follow these steps to set environment variables:
1. In the Windows search bar, start typing “environment variables” and select the first match which opens up **System Properties** dialog.

2. On the **System Properties** window, press **Environment Variables** button.

3. In the **Environment Variables** dialog:
   * Click on New under **User variables** section. Add variable name `DERBY_HOME` and value `D:\ProgramFiles\Derby\db-derby-10.14.2.0-bin` _(the path where your Derby was installed)_. Then, press OK.
   * Click on New again. Add variable name `DERBY_OPTS` and value `-Dderby.system.home=%DERBY_HOME%` and press OK.
   * Select **Path** variable and press Edit button. Press New and add `%DERBY_HOME%\bin` value and press OK.
   * Press OK to apply environment variable changes and close window.  

### Start Derby Network Server:
Start the Derby network server on the local host.  
Open **Command Prompt** or **Windows PowerShell** in **Administrator** mode and run this command: 
```
startNetworkServer -h 0.0.0.0
```
Apache Derby Network server runs on **1527** port by default.  

### Initialize Local Metastore:
Before starting Hive or Beeline, we need to initialize the Hive Metastore using `schematool` utility to create metastore database.

Open a command prompt and run this `schematool` command which connects to Derby Network Server and creates `metastore_db` in `D:\ProgramFiles\Derby\db-derby-10.14.2.0-bin\data` location:
```
schematool -dbType derby -initSchema -url "jdbc:derby://localhost:1527/D:\ProgramFiles\Derby\db-derby-10.14.2.0-bin\data\metastore_db;create=true" -driver org.apache.derby.jdbc.ClientDriver
```

After executing the above command, you can see that it created `metastore_db` folder in `DERBY_HOME\data` location.  

### Configure Hive Site:
Next, we need to add Derby Network database configuration settings in `hive-site.xml` file for Hive to connect to remote metastore.  

Go to `%HIVE_HOME%\conf` directory, create a file named `hive-site.xml` and paste the following code in the file.  
This code is referring to `metastore_db` in `DERBY_HOME\data` location. 
```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>

  <property> 
	<name>javax.jdo.option.ConnectionURL</name> 
	<value>jdbc:derby://localhost:1527/D:\ProgramFiles\Derby\db-derby-10.14.2.0-bin\data\metastore_db;create=true</value> 
	<description>JDBC connect string for a JDBC metastore</description>
  </property>  
  <property> 
	<name>javax.jdo.option.ConnectionDriverName</name> 
	<value>org.apache.derby.jdbc.ClientDriver</value> 
	<description>Driver class name for a JDBC metastore</description>
  </property>  
  
</configuration>
```  

### Copy Derby Libraries:
For Hive to communitate with local Derby, we need to copy `derbyclient.jar` and `derbytools.jar` files from `%DERBY_HOME%\lib` directory and paste them into `%HIVE_HOME%\lib` directory. 

### Start Hive CLI:
Start the hive command line interface and run a sample query (For example, `show databases;`).
```
hive
show databases;
```
**Note:** After running a query, if you receive **`FAILED: HiveException java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient`** error then 
make sure `metastore_db` is initialized properly. If not done properly, **stop Derby Network server**, **delete metastore database**, reinitialize it using `schematool` utility, **start Derby Network server** 
and then **start hive**.  

### Run Queries on Hive CLI:
On `hive>` prompt, run the following queries to create a database, table and insert data.
```
create database hive_local_derby_db;
show databases;
use hive_local_derby_db;
create table employees(emp_id int, emp_name string, emp_salary int);
show tables;
insert into employees values (101, 'johnson',5000);
```

The above insert statement submits a MapReduce job that can be tracked in YARN UI: http://localhost:8088/cluster  
In YARN UI, you can see an application name with `insert into employees…` that was executed.
<br/>

Run these commands to verify the hive metadata database and table created and data dumped in HDFS:
```
hadoop fs -ls /user/hive/warehouse
hadoop fs -ls /user/hive/warehouse/hive_local_derby_db.db
hadoop fs -ls /user/hive/warehouse/hive_local_derby_db.db/employees
hadoop fs -cat /user/hive/warehouse/hive_local_derby_db.db/employees/000000_0
```
<br/>

The same is visible in NameNode UI: http://localhost:9870/dfshealth.html also.  

Open **NameNode UI**: 
* Go to **Utilities** tab and select **Browse the file system** option.
* Enter the directory name `/user/hive/warehouse` and you can see `hive_local_derby_db.db` folder available.   
* Click on `hive_local_derby_db.db` folder to see `employees` folder in which `000000_0` file available.
* Click on the file and select **Head the file** or **Tail the file** to see the file contents. 
* We can download this file by clicking on **Download** option.
<br/>

Since we enabled Derby to run as Network server, we should be able to make multiple Hive connections.
To test this, open another Command Prompt and start Hive shell and try to run a sample query (For example, `show databases;`).
```
hive
show databases;
```
You can see that you are able to access `metastore_db` database without any issues while other Hive session is still active and accessing the same database.

To come out of `hive>` shell, use `exit;` command.

### Beeline in Embedded Mode:
Now, its time to connect Beeline CLI.  
The Beeline works in embedded mode and remote mode. In **embedded mode**, Beeline connects to an embedded HiveServer2 and in **remote mode**, it connects to HiverServer2 service over Thrift. 
Using Beeline, we can connect to HiveServer2 running on Local or Remote server using hostname and port.

To start Beeline in embedded mode, run this command in command prompt:
```
beeline -u jdbc:hive2:// -n scott -p tiger
```
or
```
beeline -u jdbc:hive2:// scott tiger
```

After beeline is connected, we can execue queries as we do in Hive CLI:
```
show databases;
use hive_local_derby_db;
show tables;
select * from employees;
```
To come out of beeline shell, use `!quit` command.  

### Start HiveServer2 Service:
HiveServer2 must be running to connect Beeline in remote mode.

Open command prompt and start the HiveServer2 service with this command:
```
hiveserver2 --hiveconf hive.root.logger=console
```

On the console, you will see that **HiveServer2** service connected to **DERBY** metastore and started on port **10002** by default while **ThriftBinaryCLIService** started on port **10000**.

### Beeline in Remote Mode:
In Remote mode, Beeline connects to HiveServer2 over Thrift CLI server that runs on 10000 port.   
Beeline remote connection can be made as **anonymous** user or with **specific credentials**.

**Connect Beeline as anonymous user:**
```
beeline -u jdbc:hive2://localhost:10000/
```

**Connect Beeline with default `scott` user:**
```
beeline -u jdbc:hive2://localhost:10000/ -n scott -p tiger
```

In both cases, we can see an error that **`"AuthorizationException: User xxx is not allowed to impersonate scott`"**. This is because HiveServer2 does not allow impersonation by default. 

To fix the above error, open `hive_site.xml` file in `HIVE_HOME\conf` location and add the following property between `<configuration>` and `<configuration>` tag.
```
  <property> 
	<name>hive.server2.enable.doAs</name> 
	<value>false</value> 
	<description>Enable user impersonation for HiveServer2</description>
  </property>
```
 
Stop and restart HiveServer2 service using the following command
```
hiveserver2 --hiveconf hive.root.logger=console
```

Now, launch Beeline CLI in remote mode and run queries.
```
beeline -u jdbc:hive2://localhost:10000/ -n scott -p tiger
```  
<br/>

## 8. Configure Remote MySQL Metastore
Now, we will see how to configure MySQL as the remote metastore database.  

### Install MySQL Server:
First, install MySQL server from the [official MySQL Downloads](https://dev.mysql.com/downloads/installer/) website in the machine if it was not already installed.

Open the **Environment Variables** dialog, select **Path** variable under **User variables** section and press Edit button. Press New and add `MySQL Install Path\bin` value 
(for example, `D:\ProgramFiles\MySQL\MySQL Server 8.0\bin`) and press OK. Then press OK again to apply environment variable changes and close window.

_**Note:**_ MySQL runs on port **3306** by default.  

### Create Metastore DB in MySQL:
In MySQL Server, create a database for Hive metastore.

Open MySQL command prompt or Workbench.
To launch `mysql>` command prompt with `root` user, execute this command:
```
mysql -h localhost -u root -p
```

Run the following queries in MySQL server to create a database named `hive_metastore` and a new user for Hive named `hive` and password as `HiveAdmin`. 
```
CREATE DATABASE hive_metastore;
USE hive_metastore;
CREATE USER 'hive'@'localhost' IDENTIFIED WITH mysql_native_password BY 'HiveAdmin';
GRANT ALL PRIVILEGES ON hive_metastore.* TO 'hive'@'localhost';
FLUSH PRIVILEGES;
```

_**Note:**_  
Make sure that `hive_metastore` database and `hive` user are not already available in your MySQL server. 
If they are already available, use a different database name and user name to create. Otherwise, you may encounter below errors:  
**`ERROR 1007 (HY000): Can't create database 'hive_metastore'; database exists`**  
**`ERROR 1396 (HY000): Operation CREATE USER failed for 'hive'@'localhost'`**

### Download MySQL JDBC Driver:
Since Hive does not provide the JDBC driver for MySQL by default, we need to explicitly get the driver and place it in `HIVE_HOME\lib` directory.

To get the JDBC Driver for MySQL (Connector/J):
* Go to [MySQL Connectors Downloads](https://dev.mysql.com/downloads/connector/j/) website.

* Choose **Platform Independent** Operating System and download `mysql-connector-j-*.zip` file.

The latest version of zip file at the time of writing this document is `mysql-connector-j-8.4.0.zip`. You can download the file of whichever latest version that you could see.

After downloading the zip file, unzip it which creates `mysql-connector-j-*` directory. Open the directory and copy `mysql-connector-j-*.jar` file to `HIVE_HOME\lib` directory. 

### Configure Hive Site File:
The next step is to configure Metastore service to communicate with MySQL database.  
Open `hive-site.xml` file in `HIVE_HOME\conf` directory and replace the existing properties with the following properties between `<configuration>` and `</configuration` element.
```
  <property> 
	<name>javax.jdo.option.ConnectionURL</name> 
	<value>jdbc:mysql://localhost:3306/hive_metastore</value> 
	<description>JDBC connect string for a JDBC metastore</description>
  </property>
  <property> 
	<name>javax.jdo.option.ConnectionDriverName</name> 
	<value>com.mysql.jdbc.Driver</value> 
	<description>Driver class name for a JDBC metastore</description>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
    <description>Username to use against metastore database</description>
  </property>
  <property>
	<name>javax.jdo.option.ConnectionPassword</name>
	<value>HiveAdmin</value>
	<description>password to use against metastore database</description>
  </property>
  <property>
	<name>hive.metastore.uris</name>
	<value>thrift://127.0.0.1:9083</value>
	<description>Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore</description>
  </property>
```

### Initialize Metastore DB:
Now, it is required to run the `schematool` utility for creating the initial metadata DB structure in MySQL database using this command:
```
schematool -dbType mysql -initSchema
```

### Verify Metastore in MySQL:
You can verify Hive metastore created under `hive_metastore` database in MySQL.

Launch MySQL command prompt with this command. Note that we are connecting with `hive` user credentials that we created earlier.  
```
mysql -h localhost -u hive -p
```
Provide the password of `hive` user when asked.

On `mysql>` prompt, run these queries:
```
show databases;
use hive_metastore;
select * from version;
show tables;
```

You should see that Hive metastore has created tables and the version of **Hive release 3.1.0**.

### Start Hive Metastore Service:
Hive will be able to connect to remote metastore in MySQL database using Thrift URIs.
So, first start the metastore service with this command to connect to our MySQL server.
```
hive --service  metastore --hiveconf hive.root.logger=console
```
On the console, you will see that **metaserver** started on port **9083** and connected to underlying **MySQL** metastore database.

### Run Queries in Hive CLI:
Open new command prompt and start Hive CLI
```
hive
```

On `hive>` prompt, run these queries to create a database, table and load data into it.

* Create a database named `hive_mysql_db`
  ```
  show databases;
  create database hive_mysql_db;
  ```
  As soon as the above query is executed, Hive creates `hive_mysql_db.db` directory in HDFS `/user/hive/warehouse` location.  
  
  The same is visible in NameNode UI: http://localhost:9870/dfshealth.html  
  Open NameNode UI, go to **Utilities** tab and select **Browse the file system** option. 
  Enter the directory name `/user/hive/warehouse` and you can see `hive_mysql_db.db` folder available.

* Create a table named `employees_tmp` in `hive_mysql_db` database. This table is created based on the columns data in the CSV file that we are going to load.
  ```
  use hive_mysql_db;
  create table employees_tmp(employee_id int, first_name string, last_name string, email string, phone_number string, hire_date string, job_id string, salary int, commission_pct int, manager_id int, department_id int) row format delimited fields terminated by ',' tblproperties ('skip.header.line.count'='1');
  ```
  After executing this query, `employees_tmp` folder is created in `/user/hive/warehouse/hive_mysql_db.db` HDFS location.  
  
  On NameNode UI, click on `hive_mysql_db.db` folder in `/user/hive/warehouse` directory to see `employees_tmp` folder.
 
* Load data from `employees.csv` file located in the local directory `D:\Datasets` into the hive table `employees_tmp`.  
  This CSV file is available in [GitHub src location](/src) that you can download and copy to `D:\Datasets` folder in your machine.
  ```
  load data local inpath 'file:///D:\Datasets\employees.csv' into table employees_tmp;
  ```
  After executing this query, `employees.csv` file is created in `/user/hive/warehouse/hive_mysql_db.db/employees_tmp` HDFS location.  

  On NameNode UI, click on `employees_tmp` folder in `/user/hive/warehouse/hive_mysql_db.db` directory and you can see `employees.csv` file is created.
  
* Select top 5 records in `employees_tmp` table from Hive CLI.
  ```
  set hive.cli.print.header=true;
  select * from employees_tmp limit 5;
  ```
 
  We can verify records in `employees.csv` file in HDFS using this command.
  ```
  hadoop fs -cat /user/hive/warehouse/hive_mysql_db.db/employees_tmp/employees.csv
  ```
  In the NameNode UI, click on `employees.csv` file in `/user/hive/warehouse/hive_mysql_db.db/employees_temp` directory and go to **Head the file** tab to see first few records
  or **Tail the file** tab to see last few records.
  
* Create a new table called `employees` similar to `employees_tmp` table except that data type of `hire_date` column would be `date` instead of `string`.
  ```
  create table employees(employee_id int, first_name string, last_name string, email string, phone_number string, hire_date date, job_id string, salary int, commission_pct int, manager_id int, department_id int);
  ```
  After executing this query, `employees` directory is created in `/user/hive/warehouse/hive_mysql_db.db` HDFS location.
  On NameNode UI, browse for `/user/hive/warehouse/hive_mysql_db.db` folder to see `employees` folder.
  
* Load data into `employees` table from `employees_tmp` table by transforming `hire_date` column in `employees_tmp` table into the Hive accepted date format.
  ```
  insert into employees select employee_id, first_name, last_name, email, phone_number, from_unixtime(unix_timestamp(hire_date,'dd-MMM-yy'),'yyyy-MM-dd') as hire_date, job_id, salary, commission_pct, manager_id, department_id from employees_tmp;
  ```
  Note that this query submits a MapReduce job to Hadoop YARN which can be tracked using the application tracking URL provided.  
  Open Hadoop YARN UI: http://localhost:8088/cluster and we can see the application has been submitted which is running the application **`insert into employees .. from employees_tmp (Stage-1)`**.

  After the above query is completed, we can see a file named `000000_0` created in `/user/hive/warehouse/hive_mysql_db.db/employees` location.  

  On NameNode UI, click on `employees` folder in `/user/hive/warehouse/hive_mysql_db.db/` directory to see  `000000_0` file.
  
* Select top 5 records in `employees` table in Hive CLI.
  ```
  select * from employees limit 5;
  ```

  We can verify this in HDFS using this command.
  ```
  hadoop fs -cat /user/hive/warehouse/hive_mysql_db.db/employees/000000_0
  ```
  In the NameNode UI, click on `000000_0` file in `/user/hive/warehouse/hive_mysql_db.db/employees` directory and go to **Head the file** tab to see first few records
  or **Tail the file** tab to see last few records.

* Verify if the count of records between `employees_tmp` and `employees` tables are matching.  
  ```
  select count(*) from employees_tmp;
  ```
  The above query submits another job to Hadoop YARN and provides us a tracking URL in YARN UI. After query completion, we will see count of records as **50**.
  ```
  select count(*) from employees;
  ```
  After query completion, we will see count of records as **50**.

### Start HiveServer2 Service:
To connect Beeline in remote mode, HiveServer2 service must be running.  
_**Note:**_ If HiveServer2 is already running, stop it and then start it freshly because we made configuration changes in `hive_site.xml` that should be loaded into HiveServer2.

```
hiveserver2 --hiveconf hive.root.logger=console
```

During HiveServer2 startup, we will encounter error **`Internal error processing get_current_notificationEventId`** and in the metastore logs _(go to the command prompt window where metastore service was started)_, 
we can see an additional error **`User xxx is not allowed to perform this API call`**.

This is because the superuser does not have access to impersonate as Hive user.  
To resolve this issue, we need to configure proxy user in Hadoop’s `core-site.xml` file as below:
1. Open `core-site.xml` file available in `%HADOOP_HOME%\etc\hadoop` location and add the following additional properties.  
   In this configuration, make sure to replace `$superuser` with your user name displayed in the above error _(in my case, I am running Hadoop with `hp` user and the same is being displayed in the above error)_
   ```
   <property>
      <name>hadoop.proxyuser.$superuser.hosts</name>
      <value>*</value>
   </property>
   <property>
      <name>hadoop.proxyuser.$superuser.groups</name>
      <value>*</value>
   </property>
   ```
 
2. After the above configuration change is done, restart Hadoop services first. Make sure to open **Windows Command Prompt** or **PowerShell** in **Administrator** mode and run these commands.
   ```
   stop-dfs.cmd
   stop-yarn.cmd
   start-dfs.cmd
   start-yarn.cmd
   ```

3. When Hadoop services are up and running, close and start Hive Metastore and HiveServer2 using these commands.
   ```
   hive --service metastore --hiveconf hive.root.logger=console
   hiveserver2 --hiveconf hive.root.logger=console
   ```
   On `hiveserver2` command prompt, you can see that **HiveServer2** service connected to metastore running over Thrift URI **thrift://127.0.01:9083** and started on port **10002**
   and **ThriftBinaryCLIService** started on port **10000**.  

### Beeline CLI:
Use this command to connect Beeline in Remote mode as anonymous user.
```
beeline -u jdbc:hive2://localhost:10000/
```
This connects to HiveServer2 running over Thrift URI on 10000 port and connects to MySQL database internally.


After beeline is connected, we should be able execute queries as we do in Hive CLI.
```
show databases;
use hive_mysql_db;
set hive.cli.print.header=true;
select * from employees limit 5;
```

### Verify Metadata in MySQL:
We can verify the Hive metadata created for database and tables in MySQL.  
Launch `mysql` prompt by connectiving with `hive` user as below. Provide the password of `hive` user when asked.
```
mysql -h localhost -u hive -p
```

Run the following queries on `mysql>` prompt:
* Get the metadata of `hive_mysql_db` database.
  ```
  use hive_metastore;
  select * from dbs;
  ```
  It shows the DB location URI as `hdfs://localhost:9820/user/hive/warehouse/hive_mysql_db.db`.
  
* Get the metadata of tables and columns created.
  ```
  select * from tbls;
  select * from columns_v2;
  ```
  It shows `employees_tmp` and `employees` tables and their respective columns.
<br/>

## 9. Hive Web UI
Hive provides the HiveServer2 UI http://localhost:10002/ to monitor HiveServer2 service.  
This UI shows that last 25 queries executed on HiveServer2. We can also view Hive configuration, stack trace and much more in this UI.  
<br/>

## 10. HCatalog and WebHCat
**HCatalog** is a table and storage management layer for Hadoop that allows **MapReduce**, **Pig** and **Hive** users to easily read and write data on HDFS. 
HCatalog provides a relational view of data stored in HDFS. It is built on top of the Hive metastore and incorporates Hive's DDL. 
It provides read and write interfaces for Pig and MapReduce and uses Hive's command line interface for issuing data definition and metadata exploration commands.

**WebHCat** (previously called as **Templeton**) is the REST API provided to access HCatalog service. 

_**Note:**_  
We cannot start HCatalog and WebHCat server from Windows since they are not compatible for Windows and so, we will use **Cygwin** utility which allows executing Linux commands from Windows.

### Create Symbolic Link for Cygwin:
Since **Java** cannot understand **Cygwin** paths properly, we will first create symbolic links for `cygdrive` to use Cygwin utility.

Open **Command prompt** in **Administrator** mode and run the following commands:
```
d:
mkdir cygdrive
mklink /J D:\cygdrive\d\ D:\
```
Note that we created `cygdrive` directory in `D drive` since we installed Hive in this drive. If you have installed Hive in a different drive, you need to create symbolic link to that drive.

### Setup Env variables for Cygwn:
Open **Cygwin64 Terminal** and run the following commands to define environment variables.  
We can add these lines to `~/.bashrc` file so we don’t need to execute every time we open Cygwin.
```
export HADOOP_HOME='/cygdrive/d/programfiles/hadoop/hadoop-3.3.6'
export PATH=$PATH:$HADOOP_HOME/bin
export HIVE_HOME='/cygdrive/d/programfiles/hive/apache-hive-3.1.3-bin'
export PATH=$PATH:$HIVE_HOME/bin
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$HIVE_HOME/lib/*.jar
```
Make sure the above `HADOOP_HOME` and `HIVE_HOME` variables refer to the exact location where Hadoop and Hive were installed.  

Verify if the above variables are properly set with these commands:
```
echo $HADOOP_HOME
echo $HIVE_HOME
echo $HADOOP_CLASSPATH
echo $PATH
```

### Start HCatalog CLI:
We can start the HCatalog command line interface by using `hcat` file available in `HIVE_HOME\hcatalog\bin`.  

In Cygwin Terminal, run the following commands to start HCatalog:
```
cd $HIVE_HOME/hcatalog/bin
./hcat
```
 
_**Note:**_ **`hcat` commands can be issued as hive commands.**  
<br/>

Run the following `hcat` command to create `employee_sample` table in `hive_mysql_db` database:
```
./hcat -e 'create table hive_mysql_db.employee_sample(emp_id int, emp_name string)'
```

Run these queries in Hive CLI to see the newly created table.
```
use hive_mysql_db;
show tables;
```  

### Start WebHCat Server:
Start the WebHCat server using this command in Cygwin Terminal:
```
$HIVE_HOME/hcatalog/sbin/webhcat_server.sh start
```
After executing the above command, you may see **`webchat started`** message on console, but in `webchat.log` (available in the location from where ever `webhcat_server.sh` script was executed), 
you can see the below error:
<br>  
**`Server failed to start: No WebApplication provider is present`**   
<br>
This error is due to some missing configuration before starting WebHCat server. Follow the [Apache Hive documentation](https://cwiki.apache.org/confluence/display/Hive/WebHCat+InstallWebHCat#WebHCatInstallWebHCat-Requirements) on how to setup WebHCat.  

Once WebHCat service is successfully running, HCatlog resources can be accessed by REST APIs using the URI format: http://localhost:50111/templeton/v1/<resource>  
For example, use this URI to get the status of WebHCat:
```
http://localhost:50111/templeton/v1/status
```
<br/>

**Congratulations!! You have now successfully installed and configured Hive with 3 metastore modes in Windows operating system. You also got a glimpse of HCatlog and WebHCat components.**
