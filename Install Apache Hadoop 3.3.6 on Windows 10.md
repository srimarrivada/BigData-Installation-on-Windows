# Install Apache Hadoop 3.3.6 on Windows 10
**Apache Hadoop** was introduced to handle Big Data in a distributed manner with parallel computation. Hadoop follows Master-Slave architecture in which Master node communicates to Slave nodes. 
Hadoop ecosystem consists of **HDFS** (Hadoop Distributed File System), **Resource Manager** (YARN) and **Computation Engine** (MapReduce).

The core components of Hadoop include **NameNode**, **DataNode**, **ResourceManager** (including Scheduler and ApplicationManager), **NodeManager** and ApplicationMaster.

Follow these steps to install Apache Hadoop 3.3.6 release on Windows 10 operating system. 
You can also go through [this PDF document](/doc/Install%20Apache%20Hadoop%203.3.6%20on%20Windows%2010.pdf) for installation steps along with screenshots.
<br/>
<br/>
## 1. Prerequisites:
The following prerequisites need to be installed before running Hadoop.
1. **JRE 8:** Hadoop 3.x requires Java 8 runtime environment. See [Hadoop Java versions](https://cwiki.apache.org/confluence/display/HADOOP/Hadoop+Java+Versions) for details.  
We can either download just **JRE 8** (Java Runtime Environment) for Windows offline installation from the official [Java Download for Windows Offline](https://www.java.com/en/download/windows_offline.jsp) website or download the whole **JDK 8** (Java Development Kit) directly from [Oracle Java Downloads](https://www.oracle.com/java/technologies/downloads/#java8) website. For the complete JDK installation steps, look at [here](https://github.com/srimarrivada/JDK-Installation/blob/main/Install%20JDK8%20on%20Windows.md). 

2. **File Archiver:** Any file archiver such as **7zip** or **WinRAR** is needed to unzip the downloaded Hadoop binaries.  
7zip can be downloaded from the [official 7zip Downloads](https://www.7-zip.org/download.html) website and WinRAR can be downloaded from the [official RAR lab Downloads](https://www.rarlab.com/download.htm) website.
<br/>

## 2. Download Hadoop Binaries: 
Download the latest Hadoop 3.3.6 release from the [Apache Hadoop Downloads](https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.3.6) mirror website.  
Go to the [suggested location](https://dlcdn.apache.org/hadoop/common/hadoop-3.3.6/) for download from where you need to download the binary file named `hadoop-3.3.6.tar.gz` file which gets downloaded to your **Downloads** folder.

After the binary file is downloaded, unpack it using any file archiver (7zip or WinRAR) utility as below:
1. Choose the installation directory in your machine and copy `hadoop-3.3.6.tar.gz` file to that directory. Here, we are choosing the Hadoop installation directory as `D:\ProgramFiles\Hadoop`.

2. In the installation directory, right click on `hadoop-3.3.6.tar.gz` file and choose "Extract Here" or "7-Zip -> Extract Here" option which extracts a new packed file `hadoop-3.3.6.tar`.

3. Next, unpack `hadoop-3.3.6.tar` file using 7zip or WinRAR utility.

4. The tar file extraction may take few minutes to finish. At the end, we may see some warnings about symbolic links creation. Ignore those warnings since they are not related to Windows operating system.

5. After the file extraction is completed, you see a folder named `hadoop-3.3.6` which consists of Hadoop binaries and libraries.

**Note:** Hadoop by default does not provide native IO libraries to run on Windows operating system, so it is necessary to add Hadoop windows utilities that can be found at [cdarlint Winutils GitHub repository](https://github.com/cdarlint/winutils) for the corresponding Hadoop version installed.

Since we installed `hadoop-3.3.6` version, download windows utilities for Hadoop 3.3.6 version from [hadoop-3.3.6 winutils Github](https://github.com/cdarlint/winutils/tree/master/hadoop-3.3.6/bin) and copy them into `hadoop-3.3.6\bin` directory (replace files if already available).  
<br/>

## 3. Set up Environment Variables:
After installing pre-requisites and Hadoop, we should configure the below environment variables defining Hadoop and Java installation paths.  
   * `JAVA_HOME`: This is the JDK installation directory path in the machine (in our case, it is `D:\ProgramFiles\Java\jdk-1.8`). Ignore it if this is already done.
   * `HADOOP_HOME`: This is the Hadoop installation directory path in the machine (in our case, it is `D:\ProgramFiles\Hadoop\hadoop-3.3.6`)

These variables need to be added to either **User environment variables** or **System environment variables** depending on Hadoop configuration needed **for a single user** or **for multiple users**.

In this tutorial, we will add **User environment variables** since we are configuring Hadoop for a single user. If you would like to configure Hadoop for multiple users, then define System environment variables.  
<br/>

1. In the Windows search bar, start typing “environment variables” and select the first match which opens up **System Properties** dialog.

2. On the **System Properties** window, press **Environment Variables** button to setup up two environment variables as described below:

3. On the **Environment Variables** dialog:
   * Click on New under **User variables** section. Add variable name `JAVA_HOME` and value `D:\ProgramFiles\Java\jdk-1.8` _(the path where your JDK or JRE was installed)_. Then, press OK.
   * Click on New again. Add variable name `HADOOP_HOME` variable and value `D:\ProgramFiles\Hadoop\hadoop-3.3.6` _(the path where your Hadoop was installed)_. Then, press OK.
   * Select **Path** variable and press Edit button. Press New and add `%JAVA_HOME%\bin`,  `%HADOOP_HOME%\bin`, `%HADOOP_HOME%\sbin` values and press OK.
   * Press OK to apply environment variable changes and close window.
<br/>

## 4. Verify Hadoop Installation:
Open Windows PowerShell or Command Prompt and run this command to verify if Hadoop is installed properly:
```
hadoop version
```
It should display the version of Hadoop i.e **Hadoop 3.3.6** installed in the machine.
<br/>
<br/>

## 5. Configure Hadoop Cluster:
After Hadoop has been installed, we need to modify the following four files to configure the Hadoop cluster:  
`HADOOP_HOME\etc\hadoop\hdfs-site.xml`  
`HADOOP_HOME\etc\hadoop\core-site.xml`  
`HADOOP_HOME\etc\hadoop\mapred-site.xml`  
`HADOOP_HOME\etc\hadoop\yarn-site.xml`  
<br/>

### HDFS Site Configuration:  
Since Hadoop is built on Master-Slave architecture, we should first create a directory to store all master (Namenode) data and another directory to store other data (Datanode) before modifying HDFS configuraiton file.

Go to `D:\ProgramFiles\Hadoop\hadoop-3.3.6` location _(where Hadoop was installed and HADOOP_HOME variable is set to)_ :
* Create a `data` folder.
* Inside `data` folder, create `dfs` folder.
* Inside `dfs` folder, create `namenode` and `datanode` sub-folders.  

After creating these folders, the directory structure should look like below:  
`D:\ProgramFiles\Hadoop\hadoop-3.3.6\data\dfs\namenode`  
`D:\ProgramFiles\Hadoop\hadoop-3.3.6\data\dfs\datanode`  

Next, open `hdfs-site.xml` file located in `HADOOP_HOME\etc\hadoop` directory, and add the following properties within the `<configuration></configuration>` element.  
```
  <property>
	<name>dfs.replication</name>
	<value>1</value>
	<description>Replication factor</description>
  </property>
  
  <property>
	<name>dfs.namenode.name.dir</name>
	<value>file:///D:/ProgramFiles/Hadoop/hadoop-3.3.6/data/dfs/namenode</value>
	<description>Name node directory</description>
  </property>
  
  <property>
	<name>dfs.datanode.data.dir</name>
	<value>file:///D:/ProgramFiles/Hadoop/hadoop-3.3.6/data/dfs/datanode</value>
	<description>Data node directory</description>
  </property>
```

### Core Site Configuration:  
To configure the NameNode URL, open `core-site.xml` file located in `HADOOP_HOME\etc\hadoop` directory, and add the following properties within the `<configuration></configuration>` element:
```
  <property>
	<name>fs.default.name</name>
	<value>hdfs://localhost:9820</value>
	<description>HDFS NameNode URL</description>
  </property>
```

### MapReduce Site Configuration:  
To configure the MapReduce framework, open `mapred-site.xml` file located in `HADOOP_HOME\etc\hadoop` directory, and add the following properties within the `<configuration></configuration>` element:
```
  <property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
	<description>MapReduce framework name</description>
  </property>
```

### YARN Site Configuration:  
To configure the YARN site, open `yarn-site.xml` file located in `HADOOP_HOME\etc\hadoop` directory, and add the following XML code within the `<configuration></configuration>` element:
```
  <property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
	<description>Yarn Node Manager Aux Service</description>
  </property>
```
<br/>

## 6. Format NameNode:
Next, we should configure the NameNode to bring the above configuration changes into effect. 

Open Windows PowerShell in Administrator mode and execute this command:
```
hdfs namenode -format
```
It shows us a message "**Storage directory has been successfully formatted**".
<br/>
<br/>

## 7. Start Hadoop Services:
Now, we should be able to start Hadoop services.  
Open Windows PowerShell or Command Prompt as Administrator and execute these commands.

### Start Hadoop Nodes:  

```
start-dfs.cmd
```
After executing the above command, it opens up two command prompt windows - one for `namenode` and other for `datanode`. </br> 
Wait until `namenode` service says "**Quota initialization completed**" and `datanode` service says "**Successfully sent block report to namenode: localhost/127.0.0.1:9820**".

### Start Hadoop YARN:  

```
start-yarn.cmd
```
After executing the above command, it opens up two command prompt windows - one for `resourcemanager`   and other for `nodemanager`. </br>
Wait until `resourcemanager` service says "**Transitioned to active state**" and `nodemanager` service says "**Registered with ResourceManager**".

_**Note:**_
You can start all the above 4 services - `namenode`, `datanode`, `resourcemanager`, `nodemanger` - together with this command:
```
start-all.cmd
```

### Verify Services:  
Now, run the following command to make sure all services are started successfully
```
jps
```
It should display the following services:
```
NodeManager 
DataNode
ResourceManager
NameNode
Jps
```
<br/>

## 8. Run HDFS Commands
Let us run a few hdfs commands to verify if they are working without any issue.
<br/>
<br/>

**Verify File System:**  
Check the status of Hadoop file system using this command.
```
hdfs fsck /
```
It should display a message "**The filesystem under path '/' is HEALTHY**" indicating that Hadoop root file system (identified by `/`) does not have any corrupted or missing data blocks.
  
**List Contents:**  
Run this command to list all contents of the root directory `/`
```
hadoop fs -ls /
```
or
```
hdfs dfs -ls /
```

**Create Directory:**  
Run this command to create a directory named `user` under root directory `/`
```
hadoop fs -mkdir /user
```
or
```
hdfs dfs -mkdir /user
```

**Copy File:**  
Run this command to copy a file named `sample_file.txt` into HDFS at `/user` path
```
hadoop fs -copyFromLocal sample_file.txt /user
```
or
```
hdfs dfs -copyFromLocal sample_file.txt /user
```

**Remove File:**  
Remove the file named `sample_file.txt` into HDFS at `/user` path from HDFS using this command 
```
hadoop fs -rm /user/sample_file.txt
```
or
```
hdfs dfs -rm /user/<file_name>
```

Similarly, we can execute any other HDFS commands on our cluster.
<br/>
<br/>

## 9. Hadoop Web UI:
Hadoop provides three web interfaces that can be used for monitoring NameNode, DataNode and YARN resources.
* NameNode UI: http://localhost:9870/dfshealth.html
* DataNode UI: http://localhost:9864/datanode.html
* YARN UI: http://localhost:8088/cluster
<br/>

## 10. MapReduce Examples:
Hadoop MapReduce is a software framework built for writing applications to process huge amounts of data (multi-terabyte data-sets) in-parallel on large clusters (thousands of nodes) of commodity hardware in a reliable, fault-tolerant manner. 

Hadoop 3.3.6 version provides in-built MapReduce example programs such as `wordcount`, `wordmean`, `aggregatewordcount`, `sudoku`, `sort`, etc. that can be executed on Hadoop cluster. These programs are packaged under `hadoop-mapreduce-examples-3.3.6.jar` file located at `HADOOP_HOME\share\hadoop\mapreduce` directory.
<br/>
<br/>

### Run WordCount Program:  
Let us execute the `wordcount` example which counts each word in the input file.  
First, create a file named `sample_file.txt` with some random text and run the following commands to create a `input` directory and move the above file into HDFS
```
hadoop fs -mkdir /input
hadoop fs -put sample_file.txt /input
hadoop fs -ls /input
```
Next, run the `wordcount` program using the following command.
```
hadoop jar D:\ProgramFiles\Hadoop\hadoop-3.3.6\share\hadoop\mapreduce\hadoop-mapreduce-examples-3.3.6.jar wordcount /input/sample_file.txt /output/wordcount
```

### Validate Output in HDFS:  
Now, verify the output generated by the above `wordcount` program in HDFS using these commands
```
hadoop fs -ls /output/wordcount
hadoop fs -cat /output/wordcount/part-r-00000
```
We can see that the program has generated the output file `part-r-00000` in HDFS under `/output/wordcount/` directory and the file contains count of each words from the input file.

### View in NameNode UI:  
The above output is visible in NameNode UI http://localhost:9870/dfshealth.html as well. 

Open NameNode UI:
* Go to **Utilities** tab and select **Browse the file system** which displays the list of folders and files created under the root ( / ) directory in HDFS
* Click on `output` folder inside `/` folder.
* Then, click on `wordcount` folder.
* Then, click on `part-r-00000` file. Here, we can see the file information such as Block Id, Block Pool ID, Generation stamp etc.
* Click on **Head the file** tab to see the first few lines or **Tail the file** tab to see the last few lines in the `part-r-00000` file.
* Click on **Download** tab to download this file into your local system.

### View Job Details in YARN UI:  
The above `wordcount` program job details can be seen in YARN UI http://localhost:8088/cluster:

Open YARN UI:
* You can see an application name `wordcount` that was executed and finished successfully.
* Click on the application ID above to see the complete details of the application.
<br/>

## 11. Stop Hadoop Services:
Open **Windows PowerShell** or **Command Prompt** as **Administrator** and stop services.

### Stop Hadoop Nodes:  
```
stop-dfs.cmd
```

After executing the above command, it automatically closes two command prompt windows that were opened earlier for the `namenode` and `datanode` daemons.  

### Stop Hadoop YARN:  
```
stop-yarn.cmd
```

After executing the above command, it automatically closes two command prompt windows that were opened earlier for the `resourcemanager` and `nodemanager` daemons.  
<br/>

_**Note:**_
You can stop all the above 4 services - `namenode`, `datanode`, `resourcemanager`, `nodemanger` - together with this command:
```
stop-all.cmd
```

### Verify Services:  
Run the following command to make sure all services are stopped successfully:
```
jps
```
It shouldn't display any of the below services:  

NodeManager  
DataNode  
ResourceManager  
NameNode  
<br/>

With this, we can say that our Hadoop 3.3.6 version has been installed and is working successfully.
