# Install Apache Spark 3.5.1 on Windows 10 and Integrate with Jupiter Notebook

**Apache Spark** is a powerful open-source analytical and computing engine to process large scale data loads. It is heavily used in Data Engineering, Data Science and Machine learning projects 
since it offers easy programming interface in various languages such as Python, SQL, Java, Scala and R. 

Spark provides heavy processing tools such as **Spark SQL** for structured data processing, **MLib** for machine learning, **Graphx** for graph processing, **Structured Streaming** for real time data processing 
and **pandas API** to handle pandas workloads.

The key components of Spark include **Spark Driver**, **Spark Executor**, **Cluster Manager** and **Spark History Server (Web UI)**. Spark also offers various Command Line Interfaces 
such as `spark-shell` (for Scala), `pyspark` (for Python), `sparkr` (for R) and `spark-sql` (for SQL) to execute the Spark code in different programming languages.

Spark supports 4 cluster managers:

1. **Standalone Cluster:** It is the simple cluster manager where Spark manages its cluster without any external dependencies. It is easy to setup and suitable for small-scale deployments.

2. **Apache Mesos:** It is a generic cluster manager that provides efficient resource allocation and scheduling, supporting dynamic resource sharing between applications.   
   **Note:** This cluster manager support has been deprecated from Spark 3.2.0 version.

3. **Hadoop YARN:** A resource manager integrated with the Hadoop ecosystem. It provides fine grained resource allocation and scheduling, supporting multiple concurrent applications on the same cluster.

4. **Kubernetes:** An open source system for automating deployment, scaling and management of containerized applications.
</br>

The Spark applications can be run in three different modes:  
* **Local Mode** – In local mode, Spark runs all processes with in a single JVM of the machine. It is the simple mode of deployment which is mostly used for testing purposes 
and is best suited for uses who are just learning spark or testing small-scale data processing workflows.

*	**Client Mode** – This is the **default** deployment mode in which the Spark driver component runs on the client machine from where the job is submitted and connects to cluster manager for resource allocation. 
In this mode, if the machine or user session running the spark application terminates, the entire Spark application gets terminated with status fail. This mode is not ideal for Production deployments.

*	**Cluster Mode** – In this mode, the Spark driver component gets launched on any of the available nodes in the Spark cluster, instead of launching on the client machine from where the job is submitted. 
Here, even if the local machine or user session is terminated, the spark application keeps running on the cluster. This mode is mostly used for processing large data sets and best suited for Production deployments.

**Note:**  
Apache Spark downloads are pre-packaged with Hadoop libraries for users to install standalone Spark cluster. 
Spark also has “Hadoop free” download that allows us to install Spark on top of existing Hadoop cluster.
</br>

This document provides instructions to install two modes – **Standalone cluster** and **Hadoop cluster** - of **Apache Spark 3.x** with Python application programming interface 
and integrate with Jupiter Notebook to execute Spark code.  
You can also go through [this PDF document](/doc/Install%20Apache%20Spark%203.5.1%20on%20Windows%2010.pdf) for installation steps along with screenshots.  
<br/>

## 1. Prerequisites:
The following prerequisites need to be installed before installing Spark.
1. **File Archiver:** Any file archiver such as **7zip** or **WinRAR** is needed to unzip the downloaded Spark binaries.
   **7zip** can be downloaded from the [official 7zip Downloads](https://www.7-zip.org/download.html) website where as **WinRAR** can be downloaded from the [official RAR lab Downloads](https://www.rarlab.com/download.htm) website.

2. **JRE 8:** Spark 3.x requires Java 8 runtime environment.
   We can either download just **JRE 8** (Java Runtime Environment) for Windows offline installation from the official [Java Download for Windows Offline](https://www.java.com/en/download/windows_offline.jsp) 
   website or download the whole **JDK 8** (Java Development Kit) directly from [Oracle Java Downloads](https://www.oracle.com/java/technologies/downloads/#java8) website.
   For the complete JDK installation steps, look at [here](https://github.com/srimarrivada/JDK-Installation/blob/main/Install%20JDK8%20on%20Windows.md).
   
3. **Python:** To run Apache Spark with Python API, we should have Python software running in the system.
   If Python is not already available in your system, you can install it from [Python Downloads](https://www.python.org/downloads/) website but it is recommended to install
   [Anaconda Distribution](https://docs.anaconda.com/anaconda/) which is an open source software built for Python and R programming languages that are heavily used in Data Science, Data Engineer
   and Data Analytics fields. Refer to [Official Anaconda Installation Guide](https://docs.anaconda.com/anaconda/install/windows/) on how to install it.
   <br/>
   <br/>
   
## 2. Install Standalone Spark:
In this section, we will see how to setup **Standalone Spark cluster** with one master server and one worker node running locally.

### 2.1. Download Spark Binaries:
* Go to [Apache Spark Downloads](https://spark.apache.org/downloads.html) page.
* Choose the package type as **Pre-built for Apache Hadoop 3.3** and click on `spark-3.5.1-bin-hadoop3.tgz` binary file.
* You will be navigated to [spark-3.5.1 mirror website](https://www.apache.org/dyn/closer.lua/spark/spark-3.5.1/spark-3.5.1-bin-hadoop3.tgz) where click on the suggested location for 
[spark-3.5.1-bin-hadoop3.tgz](https://dlcdn.apache.org/spark/spark-3.5.1/spark-3.5.1-bin-hadoop3.tgz) file which gets downloaded to your **Downloads** folder.
* After the binary file is downloaded, choose the installation directory in your machine and copy `spark-3.5.1-bin-hadoop3.tgz` file to that directory.
  Here, we are choosing Spark installation directory as `D:\ProgramFiles\Spark`.
* Right click on `spark-3.5.1-bin-hadoop3.tgz` file and choose **7-Zip** -> **Extract Here** option which extracts a new packed file `spark-3.5.1-bin-hadoop-3.tar`.
* Next, unpack `spark-3.5.1-bin-hadoop3.tar` file using **7zip** utility.
* The tar file extraction may take few minutes to finish. After finishing, you see a folder named `spark-3.5.1-bin-hadoop3` which consists of Spark binaries and libraries.

<ins>**Note:**</ins>  
Although Spark has provided Hadoop libraries, they are not compatible to run on Windows systems by default. So, we need to get Hadoop’s native IO utilities for Windows from 
[cdarlint GitHub repository](https://github.com/cdarlint/winutils) which are available for different versions of Hadoop.

* Since we installed the latest Spark 3.5.1 version, download utilities such as `winutils.exe` and `hadoop.dll` of the latest Hadoop 3.3.6 version from
  [here](https://github.com/cdarlint/winutils/tree/master/hadoop-3.3.6/bin). 
* Create a folder named `hadoop-winutils` in your **D drive** and create a sub-folder named `bin` inside it.
* Move `winutils.exe` and `hadoop.dll` files from your downloaded location to your `D:\hadoop-winutils\bin` directory.
  
### 2.2. Setup Environment Variables:
After installing pre-requisites, Spark binaries and Hadoop utilities for Windows, we should configure the below environment variables defining Java, Hadoop and Spark default paths.

* `JAVA_HOME`: This is the JDK installation directory path in the machine _(in my machine, it is `D:\ProgramFiles\Java\jdk-1.8`)_. Ignore if this is already done.
* `HADOOP_HOME`: This is the Hadoop’s WinUtils path in the machine _(in our case, it is `D:\hadoop-winutils`)_
* `SPARK_HOME`: This is the Spark installation directory path in the machine _(in our case, it is `D:\ProgramFiles\Spark\spark-3.5.1-bin-hadoop3`)_
* `PYTHONPATH`: This is the Spark python directory path which should be `%SPARK_HOME%\python`
* `PYSPARK_PYTHON`: Set this variable to the location where your `python.exe` file is available _(in my machine, the location is `D:\ProgramFiles\Anaconda\anaconda3\python`)_. This is required for Spark to execute the Python code.

_**Note:**_  
These variables need to be added to either **User environment** variables or **System environment variables** depending on Spark configuration needed **for a single user** or **for multiple users**.  
  
In this tutorial, we will add **User environment variables** since we are configuring Spark for a single user but if you would like to configure Spark for multiple users, then define System environment variables.  
<br/>

Follow these steps to set environment variables:
1. In the Windows search bar, start typing “environment variables” and select the first match which opens up **System Properties** dialog.

2. On the **System Properties** window, press **Environment Variables** button.

3. On the **Environment Variables** dialog:
   * Press New under **User variables** section. Add variable name `JAVA_HOME` and value `D:\ProgramFiles\Java\jdk-1.8` _(the path where your Java was installed)_. Then, press OK.
   * Press New again. Add variable name `HADOOP_HOME` and value `D:\hadoop-winutils` and press OK.
   * Press New again. Add variable name `SPARK_HOME` and value `D:\ProgramFiles\Spark\spark-3.5.1-bin-hadoop3`_(the path where your Spark was installed)_ and press OK.
   * Press New again. Add variable name `PYTHONPATH` and value `%SPARK_HOME%\python` and press OK.
   * Press New again. Add variable name `PYSPARK_PYTHON` and value `D:\ProgramFiles\Anaconda\anaconda3\python` _(the path where your Python was installed)_ and press OK.
   * Select **Path** variable and press **Edit** button. Press **New** and add the following values and press OK.  
     `%JAVA_HOME%\bin`  
     `%HADOOP_HOME%\bin`  
     `%SPARK_HOME%\bin`  
   * Press OK to apply environment variable changes and close window.

### 2.3. Verify Spark Installation:
Open either **Command Prompt** or **Windows PowerShell** prompt and run the following command:
```
pyspark --version
```

After the above command is executed, you can see the installed **Spark version 3.5.1**.
<br/>

### 2.4. Start Spark Services:
First, let us start the Spark Master and Worker nodes in the local system.

#### <ins>Start Spark Master:</ins>
The master node is responsible for coordinating the work among the worker nodes in the Spark cluster.

Open either **Command Prompt** or **Windows PowerShell** prompt and run the following command to start the Spark Master service:
```
spark-class org.apache.spark.deploy.master.Master
```

After executing the above command, you can see that Spark master is started at `spark://<ipaddress>:7077` i.e listening on default port `7077` and MasterWebUI is started at `http://<ipaddress>:8080` by default.

_**Note:**_  
We can change the Master node to listen, port to listen and web UI port by specifying additional options while starting Master server.  
Use the following command to get the additional options for starting master server:
```
spark-class org.apache.spark.deploy.master.Master --help
```

Verify if the master node is running correctly by accessing the Spark Master web UI at http://localhost:8080/.

On the Master Web UI, you can see the Spark Master service is running at URL `spark://<ipaddress>:7077` and worker nodes as 0 _(since Spark worker service is not started yet)_.

#### <ins>Start Spark Worker:</ins>
The worker nodes are responsible for executing tasks assigned by the Spark master. 

Open a new **Command Prompt** or **Windows PowerShell** and run the following command to start the Spark Worker service (Make sure that you replace `<ipaddress>` with IP address mentioned in your Spark Master URL)
```
spark-class org.apache.spark.deploy.worker.Worker spark://<ipaddress>:7077
```

After executing the above command, you can see that Spark worker is started at `<ipaddress>:45607` (on a random port) with available number of cores and RAM running on the machine. 
WorkerWebUI is started at `http://<ipaddress>:8081` by default.

_**Note:**_   
We can specify the number of cores to use and memory to use for Worker node, also change the Worker node to listen, port to listen and web UI port by specifying additional options while starting Worker server.  
Use the following command to get the additional options for starting worker server:
```
spark-class org.apache.spark.deploy.worker.Worker --help
```

For example, to start the Worker node with 2 cores and 8GB RAM, use this command:
```
spark-class org.apache.spark.deploy.worker.Worker -c 2 -m 8G spark://<ipaddress>:7077
```

Verify if the worker node is connected to the Spark master correctly by accessing the Spark Worker web UI at http://localhost:8081/.  
On the Worker Web UI, click on **Back to Master** link to open the Master UI where you can see one Alive Worker is available.
<br/>

### 2.5. Spark Examples:
Spark provides some in-built example programs such as `pi`, `wordcount`, `sort`, etc. available under `SPARK_HOME\examples` directory that can be executed on Spark cluster.

#### <ins>Java SparkPI Program</ins>:
Let us execute the example Java program `SparkPi` packaged under `spark-examples*.jar` file that is located in `SPARK_HOME\examples\jars` directory.

**Run In Local Mode**:  
Open a new **Command Prompt** and run the below command which executes `SparkPi` application in local mode with all available cores:
```
spark-submit --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
or
```
spark-submit --master local[*] --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```

After executing the above command, it displays the output of approximate **Pi** value. 

**Note:**  
By default, Spark displays **INFO** logging on the console.  
Follow the below steps to turn off the unnecessary INFO logging:  
* Go to `SPARK_HOME\conf` folder, take the copy of `log4j2.properties.template` file and rename as `log4j2-spark.properties`. 
* Open `log4j2-spark.properties` and change the `rootLogger.level` value from `info` to `warn`.
* Execute the below `spark-submit` command and it will not display any INFO level logging on the console.
```
spark-submit --master local[*] --driver-java-options "-Dlog4j.configurationFile=log4j2-spark.properties" --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
 
**Run In Client Mode:**  
To run the `SparkPi` application in client mode, use the below command _(Make sure to replace `<ipaddress>` value below with the IP address mentioned in your Spark Master URL)_
```
spark-submit --master spark://<ipaddress>:7077 –-deploy-mode client --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
or
```
spark-submit --master spark://<ipaddress>:7077 --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```

If you want to skip the INFO logging on console, use the extra property `--driver-java-options "-Dlog4j.configurationFile=log4j2-spark.properties"` in the `spark-submit` command as below:
```
spark-submit --master spark://<ipaddress>:7077 --driver-java-options "-Dlog4j.configurationFile=log4j2-spark.properties" --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
 
On the Master Web UI http://localhost:8080, you will notice that `Spark Pi` application has been submitted and completed.

**Run In Cluster Mode:**  
To run the `SparkPi` application in cluster mode, use the below command _(Make sure to replace `<ipaddress>` value below with the IP address mentioned in your Spark Master URL)_
```
spark-submit --master spark://<ipaddress>:7077 –-deploy-mode cluster –-class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
 
On the console, you will see that the Spark application has been submitted as Driver and once the Driver started running, it exited the `spark-submit` JVM. 
The further application status can be tracked on the Master Web UI at http://localhost:8080.

On the Master Web UI, you will notice that a new application ID has been submitted and finished and additional application submission details are found under **Completed Drivers** section. 

To see the application output, go to the Worker Web UI http://localhost:8081/ and click on **stdout** link under **Finished Drivers** section. 
On the stdout log page, you will see the output as `Pi is roughly 3.135`.

**Note:**  
Follow the below steps to skip INFO logging on console while executing Spark in cluster mode:
* Go to `SPARK_HOME\conf` folder, take the copy of `log4j2.properties.template` file and rename as `log4j2.properties`. 
* Open `log4j2.properties` file and change the `rootLogger.level` value from `info` to `warn`.
* Then, execute the `spark-submit` command with `cluster` deploy mode and INFO messages will not be displayed on the console.
```
spark-submit --master spark://<ipaddress>:7077 –-deploy-mode cluster –-class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```

#### <ins>Python wordcount Program:</ins>
Let us execute the Python program `wordcount` which counts the occurrences of each word in the given input file. This program is located in `SPARK_HOME\examples\src\main\python` directory.

First, create an input file named `sample_para.txt` with some random text and place it in your `D:\Datasets` folder.
Now, we can run the `wordcount.py` program in either local mode or client mode or cluster mode.

**Run In Local Mode:**  
Use this command to execute the `PythonWordCount` application in local mode with all available cores:
```
spark-submit --master local[*] %SPARK_HOME%\examples\src\main\python\wordcount.py D:\Datasets\sample_para.txt
```
or
```
spark-submit %SPARK_HOME%\examples\src\main\python\wordcount.py D:\Datasets\sample_para.txt
```

After the above command is executed, it displays the count of each word  available in the input file on the console.

**Note:**  
While executing Python programs in Spark, you may receive error **SparkException: Python worker exited unexpectedly (crashed)**. This error mostly occurs due to the latest Python version 3.12. 

Follow the below steps to resolve this error:
* Install the lower version of Python (say 3.11.9) from the official python downloads website _(I installed Python 3.11.9 version in `D:\ProgramFiles\Python\Python311` folder)_.
* Open **Environment Variables** dialog, select `PYSPARK_PYTHON` variable under **User variables** tab and press Edit button.
* Update the value to the new location where your older version of `python.exe` file is available _(in my case, it is `D:\ProgramFiles\Python\Python311\python`)_. Press OK and then OK to apply changes.
* Open a new command prompt and start executing PySpark programs.

**Run In Client Mode:**  
Use this command to execute the `PythonWordCount` application in client mode _(Make sure that you replace `<ipaddress>` value below with the IP address mentioned in your Spark Master URL)_
```
spark-submit --master spark://<ipaddress>:7077 -–deploy-mode client %SPARK_HOME%\examples\src\main\python\wordcount.py D:\Datasets\sample_para.txt
```
or
```
spark-submit --master spark://<ipaddress>:7077 %SPARK_HOME%\examples\src\main\python\wordcount.py D:\Datasets\sample_para.txt
```

After the above command is executed, it displays the count of each word available in the input file on the console.
 
On the Master Web UI http://localhost:8080, you can see that `PythonWordCount` application has been submitted and completed.

**Run In Cluster Mode:**  
Use this command to execute the `PythonWordCount` application in cluster mode _(Make sure that you replace `<ipaddress>` value below with the IP address mentioned in your Spark Master URL)_
```
spark-submit --master spark://<ipaddress>:7077 –-deploy-mode cluster %SPARK_HOME%\examples\src\main\python\wordcount.py D:\Datasets\sample_para.txt
```
 
However, it throws a **SparkException** because Spark does not support Cluster deploy mode of Python applications on Standalone clusters.
<br/>
<br/>

## 3. Install Spark on Hadoop:
Now, we will see how to install Spark on top of existing Hadoop cluster. 

### 3.1. Verify Hadoop Installation:
If you do not have Hadoop running, refer [here](https://github.com/srimarrivada/BigData-Installation-on-Windows/blob/main/Install%20Apache%20Hadoop%203.3.6%20on%20Windows%2010.md) to install Hadoop 
on Windows operating system.

Open **Windows PowerShell** or **Command Prompt** and run the following command:
```
hadoop version
```

### 3.2. Download Spark Binaries:
* Go to [Apache Spark Downloads](https://spark.apache.org/downloads.html) page.
* Choose the package type as **Pre-built with user-provided Apache Hadoop** and click on `spark-3.5.1-bin-without-hadoop.tgz` binary file.
* You will be navigated to [spark-3.5.1 mirror website](https://www.apache.org/dyn/closer.lua/spark/spark-3.5.1/spark-3.5.1-bin-without-hadoop.tgz) where click on the suggested location for 
[spark-3.5.1-bin-without-hadoop.tgz](https://dlcdn.apache.org/spark/spark-3.5.1/spark-3.5.1-bin-without-hadoop.tgz) file which gets downloaded to your **Downloads** folder.
* After the binary file is downloaded, choose the installation directory in your machine and copy `spark-3.5.1-bin-without-hadoop.tgz` file to that directory.
  Here, we are choosing Spark installation directory as `D:\ProgramFiles\Spark`.
* Right click on `spark-3.5.1-bin-without-hadoop.tgz` and choose **7-Zip** -> **Extract Here** option which extracts a new packed file `spark-3.5.1-bin-without-hadoop.tar`.
* Next, unpack `spark-3.5.1-bin-without-hadoop.tar` file using **7zip** utility.
* The tar file extraction may take few minutes to finish. After finishing, you see a folder named `spark-3.5.1-bin-without-hadoop` which consists of Spark binaries and libraries.

### 3.3. Set up Environment Variables:
After installing Spark binaries, we should configure the below environment variables defining the Spark default paths.
* `SPARK_HOME`: This is the Spark installation directory path in the machine _(in our case, it is `D:\ProgramFiles\Spark\spark-3.5.1-bin-without-hadoop`)_
* `PYTHONPATH`: This is the Spark python directory path which should be `%SPARK_HOME%\python`
* `PYSPARK_PYTHON`: Set this variable to the location where your `python.exe` file is available _(in my machine, it is `D:\ProgramFiles\Anaconda\anaconda3\python`)_.
   This is needed for Spark to execute the Python code.

These variables need to be added to either **User environment** variables or **System environment variables** depending on Spark configuration needed **for a single user** or **for multiple users**.  
  
In this tutorial, we will add **User environment variables** since we are configuring Spark for a single user but if you would like to configure Spark for multiple users, then define System environment variables.  
<br/>

Follow these steps to set environment variables:
1. In the Windows search bar, start typing “environment variables” and select the first match which opens up **System Properties** dialog.

2. On the **System Properties** window, press **Environment Variables** button. 

3. On the **Environment Variables** dialog:
   * Press New under **User variables** section. Add variable name `SPARK_HOME` and value `D:\ProgramFiles\Spark\spark-3.5.1-bin-without-hadoop`_(the path where your Spark was installed)_ and press OK.
   * Press New again. Add variable name `PYTHONPATH` and value `%SPARK_HOME%\python` and press OK.
   * Press New again. Add variable name `PYSPARK_PYTHON` and value `D:\ProgramFiles\Anaconda\anaconda3\python` _(the path where your Python was installed)_ and press OK.
   * Select **Path** variable and press Edit button. Press New and add `%SPARK_HOME%\bin` value and press OK.
   * Press OK to apply environment variable changes and close window.

### 3.4. Configure Spark Environment:
To use Spark's "Hadoop Free" build i.e .to use Spark on top of existing Hadoop cluster, we need to set `SPARK_DIST_CLASSPATH` environment variable in `SPARK_HOME\conf\spark-env.cmd` file.
Go to `SPARK_HOME\conf` location, create `spark-env.cmd` file and add the following lines
```
@echo off

@rem verify HADOOP_HOME env variable as a prereq
if not defined HADOOP_HOME (
	echo "HADOOP_HOME needs to be defined to point at the hadoop installation"
	exit /b 1
)

@rem set Hadoop config directory
set HADOOP_CONF_DIR=%HADOOP_HOME%\etc\hadoop

@rem need to use existing Hadoop's classpath
set HADOOP=%HADOOP_HOME%\bin\hadoop.cmd
if not exist %HADOOP% (
	echo "Missing hadoop installation: %HADOOP_HOME%\bin\hadoop.cmd must exist"
	exit /b 1
)

for /f %%a IN ('%HADOOP% classpath') do (
  set SPARK_DIST_CLASSPATH=%%a
)
```

### 3.5. Verify Spark Installation:
Open **Windows Command prompt** or **Windows PowerShell** prompt and run the following command:
```
pyspark --version
```

After the above command is executed, you can see the installed Spark version 3.5.1.
<br/>

### 3.6. Start Hadoop Services:
Open **Windows PowerShell** or **Command Prompt** as **Administrator** and start services.

#### <ins>Start Hadoop Nodes:</ins>
Run the following command to start the Hadoop nodes
```
start-dfs.cmd
```

After executing the above command, it opens up two command prompt windows - one for `namenode` and other for `datanode`. 
Wait until `namenode` service says **Quota initialization completed** and `datanode` service says **Successfully sent block report to namenode: localhost/127.0.0.1:9820**.

#### <ins>Start Hadoop YARN:</ins>
Next, start the Hadoop YARN services using the following command
```
start-yarn.cmd
```
 
After executing the above command, it opens up two command prompt windows - one for `resourcemanager` and other for `nodemanager`. 
Wait until `resourcemanager` service says **Transitioned to active state** and `nodemanager` service says **Registered with ResourceManager** 

After Hadoop services are started, verify if `NameNode`, `DataNode`, `ResourceManager` and `NodeManager` services are running by executing the following command
```
jps
```

### 3.7. Set Spark YARN Directory:
To run Spark applications on YARN cluster in Windows, we need to copy Spark jar files into HDFS and set `spark.yarn.jars` property in `spark-defaults.conf` file.

First, run the following commands to create a HDFS directory `/spark/jars` and copy Spark jar files and the necessary Hadoop jar files into HDFS directory.
```
hadoop fs -mkdir -p /spark/jars
hadoop fs -put %SPARK_HOME%\jars\* /spark/jars	
hadoop fs -put %HADOOP_HOME%\share\hadoop\client\hadoop-client-api*.jar /spark/jars
hadoop fs -put %HADOOP_HOME%\share\hadoop\client\hadoop-client-runtime*.jar /spark/jars
hadoop fs -put %HADOOP_HOME%\share\hadoop\yarn\hadoop-yarn-server-web-proxy*.jar /spark/jars
```

Go to `SPARK_HOME\conf` directory, take the copy of `spark-defaults.conf.template` and rename it as `spark-defaults.conf`. Then add the following line in `spark-defaults.conf` file:
```
spark.yarn.jars		hdfs://localhost:9820/spark/jars/*
```

### 3.8. Spark Examples:
Spark provides some in-built example programs such as `pi`, `wordcount`, `sort`, etc. available under `SPARK_HOME\examples` directory that can be executed on Spark cluster.

#### <ins>Java SparkPI Program:</ins>
Let us execute the example Java program `SparkPi` packaged under `spark-examples*.jar` file that is located in `SPARK_HOME\examples\jars` directory.

**Run In Local Mode:**  
Open a new **Command Prompt** and run the below command which executes `SparkPi` application in local mode with all available cores:
```
spark-submit --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
or
```
spark-submit --master local[*] --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```

After executing the above command, it displays the output of approximate **Pi** value. 
<br/>

**Run In YARN Client Mode:**  
Use the following command to run the `SparkPi` application in client mode on YARN cluster:
```
spark-submit --master yarn –-deploy-mode client --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
or
```
spark-submit --master yarn --class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
 
On the YARN UI Web UI http://localhost:8088/cluster, you can see that `Spark Pi` application has been submitted and completed.
<br/>

**Run In YARN Cluster Mode:**  
To run the `SparkPi` application in cluster mode on YARN, use the below command:
```
spark-submit --master yarn –-deploy-mode cluster –-class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```
 
Here, you may encounter an error **Scala module 2.15.2 requires Jackson Databind version >= 2.15.0 and < 2.16.0 - Found jackson-databind version 2.12.7-1** due to the conflict between Hadoop JARs and Spark JARs. 
Note that this error occurs only in Windows operating system.

Follow the below steps to resolve the above error:
* Download the latest `slf4j-api-2.0.7.jar` file from https://mvnrepository.com/artifact/org.slf4j/slf4j-api/2.0.7
* Place `slf4j-api-2.0.7.jar` file in `%SPARK_HOME%\jars` directory.
* Copy `slf4j-api-2.0.7.jar` file from `%SPARK_HOME%\jars` to HDFS location where Spark jars are available using the below command:
  ```
  hadoop fs -put %SPARK_HOME%\jars\slf4j-api-2.0.7.jar /spark/jars
  ```
* Copy `hadoop-client-api-*.jar` and `hadoop-client-runtime-*.jar` files from `%HADOOP_HOME%\share\hadoop\client` to `%SPARK_HOME%\jars` directory in the local system
  ```
  copy %HADOOP_HOME%\share\hadoop\client\hadoop-client-api-*.jar %SPARK_HOME%\jars
  copy %HADOOP_HOME%\share\hadoop\client\hadoop-client-runtime-*.jar %SPARK_HOME%\jars
  ```
* Remove the following lines in `spark-env.cmd` file.
  ```
  for /f %%a IN ('%HADOOP% classpath') do (
    set SPARK_DIST_CLASSPATH=%%a
  )
  ```
Now, run the `SparkPi` application in cluster mode on YARN using the below command:
```
spark-submit --master yarn –-deploy-mode cluster –-class org.apache.spark.examples.SparkPi %SPARK_HOME%\examples\jars\spark-examples*.jar
```

After executing the above command, you can see that application has been submitted to YARN and FINISHED successfully. 
Since we executed the Spark application in cluster mode on YARN, the output of application can be seen in YARN application itself.

* Open YARN UI http://localhost:8088/cluster and click on the latest Application ID value.
* On the respective application details, click on **Logs** link which opens container logs.
* On the container logs, click on **stdout** link to see the output.

#### <ins>Python wordcount Program:</ins>
Let us execute the Python program `wordcount` which counts the occurrences of each word in the given input file. This program is located in `SPARK_HOME\examples\src\main\python` directory.

First, create an input file named `sample_para.txt` with some random text and place it in your `D:\Datasets` folder.
Since our Spark is running on top of Hadoop, this file needs to be moved into HDFS using the following commands:
```
hadoop fs -mkdir /spark-input
hadoop fs -put D:\Datasets\sample_para.txt /spark-input
hadoop fs -ls /spark-input
```
 
**Note:**  
To avoid INFO logging on the console while executing the `spark-submit` command, go to `%SPARK_HOME%\conf` location, copy `log4j2.properties.template` file as `log4j2.properties` 
and change `rootLogger.level` value from `info` to `warn` in `log4j2.properties` file.

**Run In Local Mode:**  
Open a new **Command Prompt** and run the below command which executes `wordcount.py` application in local mode with all available cores
```
spark-submit --master local[*] %SPARK_HOME%\examples\src\main\python\wordcount.py /spark-input/sample_para.txt
```
or
```
spark-submit %SPARK_HOME%\examples\src\main\python\wordcount.py /spark-input/sample_para.txt
```

It displayed the count of each word in `sample_para.txt` file. 
<br/>

**Run In YARN Client Mode:**  
Before executing in YARN client mode, make sure to add the following lines in `spark-env.cmd` file _(since we removed earlier to run in YARN cluster mode)_:
```
for /f %%a IN ('%HADOOP% classpath') do (
  set SPARK_DIST_CLASSPATH=%%a
)
```

Now run the following command to submit the `wordcount.py` application in client mode on YARN cluster:
```
spark-submit --master yarn –-deploy-mode client %SPARK_HOME%\examples\src\main\python\wordcount.py /spark-input/sample_para.txt
```
or
```
spark-submit --master yarn %SPARK_HOME%\examples\src\main\python\wordcount.py /spark-input/sample_para.txt
```

On the YARN UI Web UI http://localhost:8088/cluster, we can see that `PythonWordCount` application has been submitted and completed.

**Run In YARN Cluster Mode:**  
Before executing in YARN cluster mode, make sure to remove the following lines in `spark-env.cmd` file.
```
for /f %%a IN ('%HADOOP% classpath') do (
  set SPARK_DIST_CLASSPATH=%%a
)
```

Now, run the below command which executes `wordcount.py` application in cluster mode on YARN:
```
spark-submit --master yarn –-deploy-mode cluster %SPARK_HOME%\examples\src\main\python\wordcount.py /spark-input/sample_para.txt
```

Since we executed the Spark application in cluster mode on YARN, the output of application can be seen in YARN application itself. 

* Open YARN UI http://localhost:8088/cluster and click on the latest Application ID value.
* On the respective application details, click on **Logs** link which opens container logs.
* On the container logs, click on **stdout** link to see the output.
<br/>

## 4. Spark CLIs:
Apache spark provides various command line interfaces such as **PySpark Shell**, **Spark Shell**, **Spark SQL**, **Spark R**, etc. to execute the spark code in various programming languages. 

When any spark client such as `pyspark`, `spark-shell`, `spark-sql`, etc is launched or `spark-submit` is executed, it creates a Spark Context Web UI which runs on **4040** port by default. 
If the default port **4040** is occupied by other service, Spark tries to create the web interface on the next available port incremented by 1 (i.e. 4041 port). 
Even if 4041 port is also occupied by any other service, then Spark tries to open Web UI on the next available port incremented by 1.  

**Note:**  
Spark Web UI is opened only when a spark client is active and will be closed when exited from the spark client.

### 4.1. Pyspark:
Open either **Windows PowerShell** or **Command Prompt** and simply run the following command to start PySpark shell.
```
pyspark
```

**PySparkShell** application UI is available at http://localhost:4040/ in my case _(because I opened `pyspark` shell first and is active)_

### 4.2. Spark Shell:
Open another **Windows PowerShell** or **Command Prompt** and run the following command to start Spark shell which opens `scala>` terminal by default.
```
spark-shell
```

**Spark Shell** application UI is available at http://localhost:4041/ in my case _(because I opened `spark-shell` while pyspark session is still active)_

### 4.3. Spark SQL:
Open another **Windows PowerShell** or **Command Prompt** and run the following command to start Spark SQL. 
```
spark-sql
```

Here, we are encountering **Error: Failed to load class org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver** which occurs when we are running Spark version without built-in Hadoop libraries 
which do not include Spark Hive and Spark Hive Thrift Server and other dependent packages.

To resolve this issue, we need to either copy all files from jars folder in `spark-3.5.1-bin-hadoop3` version to the current `%SPARK_HOME%\jars` location 
or set the `SPARK_HOME` environment variable to the location where `spark-3.5.1-bin-hadoop3` version was installed.

Here, I am setting `SPARK_HOME` environment variable to the location where my `spark-3.5.1-bin-hadoop3` version was installed and then launching `spark-sql` using below commands:
```
set SPARK_HOME=D:\ProgramFiles\Spark\spark-3.5.1-bin-hadoop3
spark-sql
```
 
Spark SQL application UI available at http://localhost:4042/ in my case _(because I opened `spark-sql` while pyspark and spark-shell sessions are still active)_
<br/>
<br/>

## 5. Spark Web UI:
Apache Spark provides the following UIs:
1. Master web UI
2. Worker web UI
3. Application web UI

### 5.1. Master Web UI:
The master web UI is accessible at http://localhost:8080/ by default and will be available when Spark Master is running. 

Start the Master service using the below command if not already running.
```
spark-class org.apache.spark.deploy.master.Master
```

### 5.2. Worker Web UI:
The worker web UI is accessible at http://localhost:8081/ by default and will be available when Spark Worker is running. 

Start the Worker service using the below command if not already running _(Make sure that you replace <ipaddress> value below with IP address mentioned in your Spark Master URL)_
```
spark-class org.apache.spark.deploy.worker.Worker spark://<ipaddress>:7077
```

### 5.3. Application Web UI:
Each Spark application launches its own instance of the web UI. The application web UI is accessible at http://localhost:4040/jobs/ by default and will be available only when 
the Spark application is running. 
<br/>
<br/>

## 6. Spark History Server:
The Spark History Server is a web UI that displays the logging data from Spark jobs that were run with event logging enabled. It works only when files were not flushed before the Spark Master attempted 
to build a history user interface. 

Before starting the Spark History server, we need to define Spark Event Log directory and Spark History Log directory in `spark-defaults.conf` file.

**<ins>To store Spark logs on local file system:</ins>**  
Go to `%SPARK_HOME%` location in your local system and create `logs` folder and create another folder named `spark-events` inside `logs` folder.

**<ins>To store Spark logs on HDFS:</ins>**  
In HDFS, create `/spark/logs` folder and create another folder named `spark-events` inside `/spark/logs` folder using these commands:
```
hadoop fs -ls /spark
hadoop fs -mkdir /spark/logs
hadoop fs -mkdir /spark/logs/spark-events
hadoop fs -ls /spark/logs/spark-events
```
 
Now, go to `SPARK_HOME/conf` location and edit `spark-defaults.conf` file _(if this is not available, rename `spark-defaults.conf.template` to `spark-defaults.conf`)_ to enable event logging 
and specify the directory for event logs.

Depending on where you want to enable event logging, add the following lines in the `spark-defaults.conf` file and ensure that the paths mentioned in `spark.history.fs.logDirectory` and 
`spark.eventLog.dir` are valid and accessible. Otherwise, Spark History server would fail to start.

**<ins>To enable event logging on local file system:</ins>**  
```
#Sets the logging directory for the history server
spark.history.fs.logDirectory file:///D:/ProgramFiles/Spark/spark-3.5.1-bin-without-hadoop/logs

#Turns on logging for applications submitted from this machine
spark.eventLog.enabled	true
spark.eventLog.dir		file:///D:/ProgramFiles/Spark/spark-3.5.1-bin-without-hadoop/logs/spark-events
```

**<ins>To enable event logging on HDFS:</ins>**  
```
#Sets the logging directory for the history server
spark.history.fs.logDirectory	hdfs://localhost:9820/spark/logs

#Turns on logging for applications submitted from this machine
spark.eventLog.enabled	true
spark.eventLog.dir		hdfs://localhost:9820/spark/logs/spark-events
```
Since I want to enable event logging on HDFS, I added the above lines of code in `spark-defaults.conf` file. 

Now, start the Spark history server in Windows using the following command
```
spark-class org.apache.spark.deploy.history.HistoryServer
```

Once the history server is started, it's UI is accessible under default port 18080 at http://localhost:18080/

This History server is useful to track the running and completed Spark applications even if the spark application web UI is not available.

**Note:**  
When event logging is enabled, the default behavior is for all logs to be saved, which causes the storage to grow over time. 
To enable automated cleanup, edit `spark-defaults.conf` file and add the following lines to perform cleanup daily and delete logs older than 7 days.
```
spark.history.fs.cleaner.enabled true
spark.history.fs.cleaner.interval 1d
spark.history.fs.cleaner.maxAge 7d
```
</br>

## 7. Write Sample PySpark Code
Let us write some sample Pyspark code to read a CSV file and write the data into JSON format.

We will write the code to read/write CSV file in local file system as well as in HDFS.

### 7.1. Read/Write Locally:
Since our `SPARK_HOME` is currently set to `spark-3.5.1-bin-without-hadoop` version, all files are expected to be read or written to HDFS. 

To read or write files in local file system, we would need additional jar files that doesnot come with `spark-3.5.1-bin-without-hadoop` version. So, let us temporarily set the `SPARK_HOME` environment 
variable to the location where `spark-3.5.1-bin-hadoop3` version was installed and then launch pyspark CLI.

Open a new command prompt and run the following commands _(note that `spark-3.5.1-bin-hadoop3` version was installed under `D:\ProgramFiles\Spark` location)_
```
set SPARK_HOME=D:\ProgramFiles\Spark\spark-3.5.1-bin-hadoop3
pyspark
```

At the `pyspark` prompt, write the below lines of code to read a file into dataframe and displays output. 
Here, I am using the input file `departments.csv` _(you can download this file from [here](/src/departments.csv))_ available under `D:\Datasets` folder and writing into output 
location `D:\Datasets\spark-output\department-locations.json`.
```
df=spark.read.option("header",True).csv("D:\Datasets\departments.csv")
df.printSchema()
df.show(5)
df.count()
df.createOrReplaceTempView("DEPARTMENTS")
df2=spark.sql("select DEPARTMENT_ID, DEPARTMENT_NAME, MANAGER_ID, LOCATION_ID from DEPARTMENTS where LOCATION_ID <> 1700")
df2.show()
df2.write.format("json").save("D:\Datasets\spark-output\department-locations.json")
```

After executing the above code, the output will be generated in `D:\Datasets\spark-output` location.

Open Spark Web UI at http://localhost:4043 where you will see some jobs have been submitted for the code that was executed from the Pyspark shell.

### 7.2. Read/Write HDFS:
First, let us copy the input file `departments.csv` into HDFS
```
hadoop fs -put "D:\Datasets\departments.csv" /spark-input
hadoop fs -ls /spark-input
```

Next, open `pyspark` shell using below command
```
pyspark
```
 
On the `pyspark` shell, write the below lines of code to read the above input file into dataframe and displays output
```
df=spark.read.options(header=True, inferSchema=True).csv("/spark-input/departments.csv")
df.printSchema()
df.show(5, truncate=False)
df.count()
df.createOrReplaceTempView("DEPARTMENTS")
df2=spark.sql("select DEPARTMENT_ID, DEPARTMENT_NAME, MANAGER_ID, LOCATION_ID from DEPARTMENTS where LOCATION_ID <> 1700")
df2.show()
df2.write.format("json").save("/spark-output/department-locations.json")
```

Open another command prompt and run the following commands to see the output file created in HDFS.
```
hadoop fs -ls /spark-output/department-locations.json
hadoop fs -cat /spark-output/department-locations.json/part-00000-*.json
```

The same is visible in NameNode UI http://localhost:9870/dfshealth.html
* In NameNode UI, go to **Utilities** tab and select **Browse the file system** option.
* Search for `/spark-output/department-locations.json` directory where you will see `part-00000-*.json` file is available.

Open Spark Web UI at http://localhost:4043 where you can notice some jobs submitted for the code that was executed from the Pyspark shell.
<br/>
<br/>

## 8. Spark SQL:
The Spark SQL CLI is a conventional tool to run the Hive metastore service in local mode and execute queries from command line. It creates an embedded `metastore_db` directory 
(a relational database to manage the metadata of relational entities such as databases, tables, columns partitions) in the location from where ever `spark-sql` is started.

Since `spark-sql` command line interface requires hive libraries that comes with `spark-3.5.1-bin-hadoop3` version, make sure that your `SPARK_HOME` environment varaible is set to the location 
where `spark-3.5.1-bin-hadoop3` version was installed.

Open a new **command prompt** in **Administrator** mode and run the following command:
```
spark-sql --master local[2] --executor-memory 1G --driver-memory 1G --conf spark.sql.warehouse.dir="file:///d:/tmp/spark-warehouse"
```
Note that we have set `spark.sql.warehouse.dir` property to `D:\tmp\spark-warehouse` where Spark SQL stores all relational entities such as databases, tables, columns, etc.

As soon as `spark-sql` is launched, you can see that `metastore_db` directory and `derby_log` file is created in the location from where it was launched.

On the `spark-sql>` prompt, let us run some Hive queries to create a database, table and load data into it.

* Create a database named `temp_db`:
  ```
  create database temp_db;
  show databases;
  ```
  As soon as the above queries are executed, we can see that spark-warehouse directory is created at D:\tmp location and temp_db.db folder is created under spark-warehouse directory.
 
* Create a table named `employees` in `temp_db` database:
  This table is created based on the columns data in the CSV file that we are going to load:
  ```
  use temp_db;
  create table employees(employee_id int, first_name string, last_name string, email string, phone_number string, hire_date string, job_id string, salary int, commission_pct int, manager_id int, department_id int) row format delimited fields terminated by ',' tblproperties ('skip.header.line.count'='1');
  ```
  After the above queries are executed you can see `employees` folder is created in `D:\tmp\temp_db.db` location in local system.

* Verify the format of the `employees` table created:
  ```
  describe formatted employees;
  ```

* Load data from `employees.csv` file located in the local directory `D:\Datasets` into the hive table `employees`:

  **Note:** This CSV file is available in [here](/src/employees.csv) that you can download and copy to `D:\Datasets` folder in your machine.
  ```
  load data local inpath 'file:///D:/Datasets/employees.csv' into table employees;
  ```
  After executing the above query, you will see that `employees.csv` file created in `D:\tmp\temp_db.db\employees` directory.

* Select top 5 records in `employees` table from Hive CLI:
  ```
  set spark.hadoop.hive.cli.print.header=true;
  select * from employees limit 5;
  ```
  **Note:** By default Spark SQL will not skip headers although we set the table property `tblproperties ('skip.header.line.count'='1')`.

Similarly, we can run any queries from `spark-sql` CLI that connects to Hive metastore database running locally.
<br/>
<br/>

## 9. Spark Beeline:
Spark provides `beeline` CLI that runs on Thrift JDBC/ODBC server (corresponds to `hiveserver2` service in built-in Hive). Spark SQL can also act as a distributed query engine using its JDBC/ODBC server. 

Since `beeline` command line interface requires hive libraries that comes with `spark-3.5.1-bin-hadoop3` version, make sure that your `SPARK_HOME` environment varaible is set to the location 
where `spark-3.5.1-bin-hadoop3` version was installed.

To start using Spark provided beeline, we need to first start the Spark Master, Worker and Thrift JDBC/ODBC services.

### 9.1. Start Spark Master:
Open a new **Command Prompt** or **Windows PowerShell** and run the following command to start the **Spark Master** service:
```
spark-class org.apache.spark.deploy.master.Master
```
Here, we can see that Spark master is started at `spark://<ipaddress>:7077` i.e listening on default port 7077 and MasterWebUI is started at `http://<ipaddress>:8080` by default.

**Note:**  
If you are unable to see any logging on the console, then open `log4j2.properties` file in `SPARK_HOME\conf` location and change the `rootLogger.level` property to `info` value.

### 9.2. Start Spark Worker:
Open a new **Command Prompt** or **Windows PowerShell** and run the following command to start the **Spark Worker** service 
_(Make sure that you replace `<ipaddress>` value below with IP address mentioned in your Spark Master URL)_
```
spark-class org.apache.spark.deploy.worker.Worker spark://<ipaddress>:7077
```

Here, we can see that Spark worker is started at `<ipaddress>:3884` (on a random port) with available number of cores and RAM running on the machine. WorkerWebUI is started at `http://<ipaddress>:8081` 
by default.

### 9.3. Start Spark Thrift Server:
Spark Thrift services tries to access (or create if not available) an embedded `metastore_db` directory in the location from where ever it is started. 

Open a new **Command Prompt** or **Windows PowerShell** and run the following command to start the Spark Thrift service 
_(Make sure that you replace `<ipaddress>` value below with IP address mentioned in your Spark Master URL)_
```
spark-submit --class org.apache.spark.sql.hive.thriftserver.HiveThriftServer2 --master spark://<ipaddress>:7077
```

After executing the above command, you will see that Spark **ThriftBinaryCLI** service is started on port 10000 and **HiveServer2** and **HiveThriftServer2** services are also started.

### 9.4. Start Beeline:
Open a new **Command Prompt** or **Windows PowerShell** and run the following command to start the beeline CLI:
```
beeline
```
After executing the above command, you can see the **Beeline version 2.3.9**.

Now, connect to the JDBC/ODBC thrift server in beeline using the following command. When Beeline asks for a username and password, simply enter the username of your machine and a blank password.
```
!connect jdbc:hive2://localhost:10000
```

After executing the above command, you will notice that Beeline has been successfully connected to Thrift server which is running on 10000 port.

Now, we can run Spark queries at `beeline>` prompt as below:
```
show databases;
use temp_db;
show tables;
select * from employees limit 5;
```

Use this command to exit out of beeline shell:
```
!quit
```
<br/>

## 10. Spark with Jupiter Notebook:
Now, we will see how to integrate **Pyspark** with **Jupiter Notebook** available in Anaconda installation so that we can execute Spark code on Jupiter Notebook directly.

First, make sure that Anaconda `scripts` location is added to `PATH` environment variable.
* Open **Environment Variables** dialog and select `PATH` variable under **User Variables** and press Edit button. 
* Then press New and add the value `D:\ProgramFiles\Anaconda\anaconda3\Scripts` _(the path where your Anaconda distribution was installed)_ and press OK.
* Then press OK to close **Environment Variables** dialog.

Now, open a new **command prompt** and set the following environment variables for pyspark to execute Jupyter.
```
set PYSPARK_DRIVER_PYTHON=jupyter
set PYSPARK_DRIVER_PYTHON_OPTS="notebook --NotebookApp.open_browser=True --NotebookApp.ip='localhost' --NotebookApp.port=8888"
```
 
**Note:** 
If you have installed Spark on a Virtual Machine and would like to access Jupyter from your host browser, you should set the `NotebookApp.ip` flag to `--NotebookApp.ip='0.0.0.0'` in the above command 
so that your VM's Jupyter server will accept external connections. You can then access Jupyter notebook from the host machine on port 8888.

Execute pyspark with the following command:
```
pyspark --master local[*] --executor-memory 1G --driver-memory 1G --conf spark.sql.warehouse.dir="file:///D:/tmp/spark-warehouse" --packages com.amazonaws:aws-java-sdk-pom:1.10.34 --packages org.apache.hadoop:hadoop-aws:2.8.0
```
Here, we are executing `pyspark` locally (local master) with all cores of the computer and setting the Spark Executor memory to 1GB and Spark Driver memory to 1GB and setting the Spark SQL Warehouse directory
to `D:\tmp\spark-warehouse` in local system to store Spark SQL data. We are also setting specific packages such as `amazonaws:aws-java-sdk-pom` and `org.apache.hadoop:hadoop-aws` to pyspark to load.
These pacakges are necessary to access AWS S3 repositories from Spark.

**Note:**  
Sometimes you may encounter an error _**Jupyter command `jupyter-notebook --NotebookApp.open_browser=True --NotebookApp.ip='localhost' --NotebookApp.port=8888` not found**_ while executing the pyspark.

To resolve the above error, set `PYSPARK_DRIVER_PYTHON_OPTS` to `notebook` and execute pyspark with these commands:
```
set PYSPARK_DRIVER_PYTHON_OPTS="notebook"
pyspark --master local[*] --executor-memory 1G --driver-memory 1G --conf spark.sql.warehouse.dir="file:///D:/tmp/spark-warehouse" --packages com.amazonaws:aws-java-sdk-pom:1.10.34 --packages org.apache.hadoop:hadoop-aws:2.8.0
```
After executing the above commnd, it opens Jupyter application URL http://localhost:8888/tree? 
* In Jupiter Application, press **New** button and select **Notebook** option.
* It opens an **Untitled** ipynb application where you can execute the python code that runs on Spark engine.
* Execute the following lines of code on the Jupyter notebook to create a Spark Session, read a CSV file `D:\Datasets\departments.csv` into a DataFrame and display the schema of DataFrame and its data.
  
  ```
  import pyspark as ps
  spark = ps.sql.SparkSession.builder.getOrCreate()
  df=spark.read.option("header",True).csv("D:\Datasets\departments.csv")
  df.printSchema()
  df.show()
  ```
  It will dispaly the data available in the Dataframe that read from the `departments.csv` file.
<br/>
  
**Congratulations!! You have successfully installed Spark 3.5.1 version as a Standalone as well as on Hadoop cluster with Python API implementation and executed Python Spark code and queries on Spark SQL and Beeline. You also got an exposure on how to integrate the Spark engine with Jupiter Notebook provided by Anaconda distribution.**
