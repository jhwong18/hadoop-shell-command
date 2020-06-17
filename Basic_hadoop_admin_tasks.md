## Hadoop Administrative tasks
Basic Hadoop Administrative tasks for various services: Hadoop, HDFS, YARN, Flink, HBase, OpenTSDB.

### Table of Contents
##### Various Issues in Memory Storage
- [How to maintain Disk Free Space in OS (Logs space and deleting old logs)](#OSspace)
- [How to resolve high CPU Utilization](#cpu)
- [How to resolve Low Swap Space](#swap)
- [How to resolve low HDFS disk space alert](#hdfs)

##### HDFS / Hadoop
- [How to monitor Hadoop Cluster health](#hadoop)
- [Finding log files in HDFS](#hdfslog)
- [How to monitor and handle Alerts & Warnings in Ambari](#alerts)
- [Using Web UI to monitor NameNode](#namenode)


##### HBase
- [How to use Web UI to monitor Hbase Master](#hbase)
- [Finding log files in HBase](#hbaselog)
- [How to monitor Region Servers health](#regionservers)


##### OpenTSDB
- [How to monitor Stats and Data loading in OpenTSDB](#opentsdb)
- [Finding log files in OpenTSDB](#tsdblog)
- [Check Connection/Latency between OpenTSDB and Hbase](#tsdbconnection)


##### YARN
- [Using web UI to monitor jobs in Yarn Resource Manager](#yarn)
- [Finding log files in YARN](#yarnlog)
- [Manage services on YARN via CLI](#yarncli)

##### Flink
- [Flink Dashboard - Flink jobs monitoring](#flink)


##### Phoenix
- [Monitoring Metrics in Phoenix](#phoenix) 


#### Basic Tasks
- [Start, Stop, Restart Service](#restart)



### Maintain disk free space in OS Space (Logs space and deleting old logs)
<a name="OSspace">
  
Low logical Disk Free Space is due to the lack of storage space in either of the following folders:
	
- /opt
- /tmp
- /var/log
- /var/lib


| Folder | Description |
| :---: | :---: |
| /opt |	/opt is reserved for the installation of add-on application software packages | 
| /tmp |	The /tmp directory must be made available for programs that require temporary files. Programs must not assume that any files or directories in /tmp are preserved between invocations of the program. | 
| var/cache |	Application cache data |
| var/lib |	Variable state information |
| var/local |	Variable data for /usr/local |
| var/lock |	Lock files |
| var/log |	Log files and directories |
| var/opt |	Variable data for /opt |
| var/run |	Data relevant to running processes |
| var/spool |	Application spool data |
| var/tmp |	Temporary files preserved between system reboots |


The steps to resolve low Logical Disk Free Space:
  1) Find out the disk usage summary across the different folders
  
  ```
  du -sh *
  du -sh /opt/
  du -sh /var/
  du -sh /tmp/
  ```
  
  2) Check the disk free space available on each folder
  
  ```
  df -h /var/
  ```
  
  3) If the remaining disk free space is low, proceed to remove the files in the folder where it occupies the most space. (/opt, /var, /tmp)
  
  ```
  rm /tmp/*    # Remove all files in /tmp folder 
  rm /tmp/*.txt  # Remove all files in specific extension
  
  cd /tmp/
  find -type f -name '*text*' -delete  # Remove all files with specific text
  ```

  4) Run the disk free command to check the free space in the respective folders
  
  ```
  df -hT /opt/
  df -hT /tmp/
  free
  ```




### CPU Utilization
<a name="cpu">
  
A common issue encountered would be a high CPU utilization. The first step is to check the current CPU utilization

```
iostat -c 
```

**Enable CPU Scheduling in** `capacity-scheduler.xml`

CPU scheduling is not enabled by default. To enable the CPU Scheduling, set the following property in the /etc/hadoop/conf/capacity-scheduler.xml file on the ResourceManager and NodeManager hosts:

Replace the `DefaultResourceCalculator` with the `DominantResourceCalculator`.

Property:`yarn.scheduler.capacity.resource-calculator`

Value: `org.apache.hadoop.yarn.util.resource.DominantResourceCalculator`

```
<property>
 <name>yarn.scheduler.capacity.resource-calculator</name>
 <!-- <value>org.apache.hadoop.yarn.util.resource.DefaultResourceCalculator</value> -->
 <value>org.apache.hadoop.yarn.util.resource.DominantResourceCalculator</value>
</property>
```

**Set Vcores in yarn-site.xml**

In YARN, vcores (virtual cores) are used to normalize CPU resources across the cluster. The `yarn.nodemanager.resource.cpu-vcores` value sets the number of CPU cores that can be allocated for containers.

The number of vcores should be set to match the number of physical CPU cores on the NodeManager hosts. Set the following property in the /etc/hadoop/conf/yarn-site.xml file on the ResourceManager and NodeManager hosts:

Property: `yarn.nodemanager.resource.cpu-vcores`

Value: <number_of_physical_cores>

Example:

```
<property>
 <name>yarn.nodemanager.resource.cpu-vcores</name>
<value>16</value>
</property>
It is also recommended that you enable CGroups along with CPU scheduling. CGroups are used as the isolation mechanism for CPU processes. With CGroups strict enforcement turned on, each CPU process gets only the resources it asks for. Without CGroups turned on, the DRF scheduler attempts to balance the load, but unpredictable behavior may occur.
```




### Low Swap Space
<a name="swap">

To check the Swap space, use the free command 

```
free
```

To reduce usage of swap space, you can minimize usage of swap by playing with swappiness. You can get current value with command:

```
cat /proc/sys/vm/swappiness
```

If you want to change it edit `/etc/sysctl.conf` and add line like

```
vm.swappiness = 1
```

(by Cloudera recommendations) and execute
```
sysctl -p 
```

Swap space can be added by creating a swap file or by increasing the amount of the swap partition. Provided the amount of available hard disk space above, we can proceed to the creation of our swap file which we will call myswapfile (with 1G of size). It will be located under root / directory . We will be using the fallocate program which will create a file with the desired size. 

Run the command below :

```
sudo fallocate -l 1G /myswapfile
sudo dd if=/dev/zero of=/myswapfile bs=1024 count=1048576    # If fallocate is not installed on your system
sudo chmod 600 /mnt/myswapfile
sudo mkswap /myswapfile
sudo swapon /myswapfile
cat /proc/swaps   # check if newly added swap file is now available
```


### HDFS disk space alert
<a name="hdfs">

To check the disk space in HDFS, run the following commands: 

```
hdfs dfs -df -h / 
hdfs dfs -du -s -h /
```

Alternatively, use dfsadmin for disk free command

```
hdfs dfsadmin -report
```

If there is insufficient disk space space in HDFS, you can remove files in HDFS or increase the spaceQuota in a particular directory.
To remove files in HDFS:

```
hadoop fs -rm <filename in HDFS>
hadoop fs -rm <filename in HDFS>
hadoop fs -rm /tmp/* # Remove all files in /tmp folder 
hadoop fs -rm /tmp/*.txt  # Remove all files in specific extension
```


You can also set the spaceQuota or file count Quota, or check status of the quotas in a directory

```
hdfs dfs -setQuota <#num> <dir>
hdfs dfs -setSpaceQuota <#space> <dir>
hdfs dfs -clrSpaceQuota <dir>
hdfs dfs -count -q <dir>"
```

### HDFS / Hadoop
<a name="hadoop">
	


### Monitor Cluster Health in Hadoop
Monitoring of Hadoop clusters can be done in 3 ways:
	- Using Web UI on Apache Hadoop
		- Namenode: http://quickstart.cloudera:50070/dfshealth.html#tab-overview 
		- Secondary Namenode: http://quickstart.cloudera:50090/status.html
	
	- Using Web UI on Ambari
		- http://[YOUR_AMBARI_SERVER_FQDN]:8080
	
	- Hadoop hdfs commands 


**Using Web UI on Apache Hadoop**

### Monitor Namenode UI
<a name="namenode">
	
On the overview of the Namenode URL, you can monitor the following metrics:

	- Heap Memory Used/Available/Maximum
	- Number of files and directories, Number of blocks
	- Capacity, Disk Free Space used/remaining
	- Number of Live/Dead nodes
	- Storage directory
	- Datanode (datanode name, admin state, capacity, DFS, failed volumes)

On the Seocndary Namenode URL, you can monitor the following metrics:
	- Last checkpoint
	- Namenode Address
	- Checkpoint Image URI
	- Checkpoint Editlog URI


**Using Web UI on Ambari**

### Handle Alerts & Warnings in Ambari
<a name="alerts">

The following link contains the resources to monitor HDFS, YARN, HBase using Ambari Web UI. 
https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_access_ambari_web.html
https://docs.cloudera.com/HDPDocuments/Ambari-2.7.1.0/managing-and-monitoring-ambari/amb_managing_and_monitoring_hadoop_cluster.pdf	
	


**Hadoop hdfs commands**

The first step is to check dfsadmin report for HDFS cluster overall status and each namenode/datanode status.

```
hdfs dfsadmin -report
```

Optionally, you can monitor using the Namenode, Datanode Web Ui Check and check datanode failure volume

```
http://<namenode ip>://50070.html#tab-overview
```

The next step is to check that we can read and write status successfully in HDFS

```
hdfs dfs -put <local file> <dir in hdfs>
hdfs dfs -cat <file in hdfs>
```

The last step is to check via fsck that system is healthy and ensure that the log file in filesystem under path / is HEALTHY.

```
hdfs fsck / -files -blocks -locations >dfs-fsck.log
```


### Log files in HDFS
<a name="hdfslog">
	
From this Apache Hadoop UI, we can access the log files, by clicking on the 'utilities' section -> 'logs' section.

Alternatively, the log files are available in the folder `/var/log/hadoop-hdfs/`. The list of log files are:

	- Datanode: `hadoop-hdfs-datanode-quickstart.cloudera.log`
	- Journalnode: `hadoop-hdfs-journalnode-quickstart.cloudera.log`
	- Namenode: `hadoop-hdfs-namenode-quickstart.cloudera.log`
	- Secondary Namenode: `hadoop-hdfs-secondarynamenode-quickstart.cloudera.log`





### Hbase
<a name="hbase">

### To start, stop, restart services

```
sudo service hadoop-hbase-regionserver stop
sudo service hadoop-hbase-regionserver start
sudo service hadoop-hbase-regionserver restart
```

```
sudo service hbase-master stop
sudo service hbase-master start
sudo service hbase-master restart
```

### Monitor Hbase Master Web UI

### Region Servers Health check
<a name="regionservers">
	

To access to the HBase Master Web UI, ensure that your hbase-master service is running. Next, enter the following url:

```
http://quickstart.cloudera:60010
```

The HBase Master web UI shows:
	- the number of requests per second being served by each of the RegionServers
	- the number of regions that are online on the RegionServers, and the used and max heap.

This is a useful place to start when you’re trying to find out the state of the system. Often, you can find issues here when RegionServers have fallen over, aren’t balanced in terms of the regions and requests they’re serving, or are misconfigured to use less heap than you had planned to give them.


### Finding log files in HBase
<a name="hbaselog">
	
From this Master Web UI, we can access the log files, by clicking on the logs section and open the log file `var/log/hbase/hbase-hbase-master-quickstart.cloudera.log`. Alternatively, you can access the logs file via
`var/log/hbase/hbase-hbase-master-quickstart.cloudera.log` (for HMaster)


The key process logs are as follows (replace <user> with the user that started the service, and <hostname> for the machine name)

	- NameNode: $HADOOP_HOME/logs/hadoop-<user>-namenode-<hostname>.log

	- DataNode: $HADOOP_HOME/logs/hadoop-<user>-datanode-<hostname>.log

	- JobTracker: $HADOOP_HOME/logs/hadoop-<user>-jobtracker-<hostname>.log

	- TaskTracker: $HADOOP_HOME/logs/hadoop-<user>-tasktracker-<hostname>.log
 
	- HMaster: $HBASE_HOME/logs/hbase-<user>-master-<hostname>.log

	- RegionServer: $HBASE_HOME/logs/hbase-<user>-regionserver-<hostname>.log
	
	
	

### OpenTSDB

### Monitor Stats and Data loading in OpenTSDB
<a name="opentsdb">

OpenTSDB offers a number of metrics about its performance, accessible from either its web GUI or HTTP API. The metrics in the stats include the number of open connections, invalid queries, latencies, etc. Some of these metrics are described below:  

| Metric | Tags | Type | Description |
| :---: | :---: | :---: | :---: |
| tsd.connectionmgr.connections |	type=open |	Gauge |	The number of currently open Telnet and HTTP connections. |
| tsd.connectionmgr.exceptions |	type=closed |	Counter |	The total number of exceptions caused by writes to a channel that was already closed. This can occur if a query takes too long, the client closes their connection gracefully, and the TSD attempts to write to the socket. This includes all Telnet and HTTP connections. |	
			
      
The description of each statistics is available in the documentation url:
http://opentsdb.net/docs/build/html/user_guide/stats.html

To access the stats on the HTTP API, use the following command: 
  - GET
  - POST

The Query String is:

```
http://localhost:4242/api/stats
```


More details on the HTTP API commands is available in the documentation url:
http://opentsdb.net/docs/build/html/api_http/stats/index.html




### Finding log files in OpenTSDB
<a name="tsdblog">

Opentsdb uses `logback.xml` file for generating the logs. If you have installed from debian package, then the tsdb binary will look for the `logback.xml` at `/etc/opentsdb/logback.xml`. The log files is also available on the web GUI, under the 'logs' tab.


| Levels in logging | Description |
| :---: | :---: |
| ERROR | Something failed, be it invalid data, a failed connection or a bug in the code. You should pay attention to these and figure out what caused the error. Check with the user group for assistance. | 
| WARN | These are often caused by bad user data or something else that was wrong but not a critical error. Look for warnings if you are not receiving the results you expect when using OpenTSDB. | 
| INFO |  Informational messages are notifications of expected or normal behavior. They can be useful during troubleshooting. Most logging appenders should be set to INFO. | 
| DEBUG |  If you require further troubleshooting you can enable DEBUG logging that will give much greater detail about what OpenTSDB is doing under the hood. Be careful enabling this level as it can return a vast amount of data. | 
| OFF |  To drop any logging messages from a class, simply set the level to OFF. | 


This appender will write to a log file called /var/log/opentsdb/opentsdb.log. When the file reaches 128MB in size, it will rotate the log to opentsdb.log.1 and start a new opentsdb.log file. Once the new log fills up, it bumps .1 to .2, .log to .1 and creates a new one. It repeats this until there are four log files in total. The next time the log fills up, the last log is deleted. This way you are gauranteed to only use up to 512MB of disk space. 


### Tuning performance in OpenTSDB
<a name="tsdbtuning">
  
  
Tuning of OpenTSDB for HBase Storage. These are parameters to look at for using OpenTSDB with Apache HBase.
  - Date Tierd Compaction
  - HBase Read/Write Queues
  - HBase Cache
  - HBase Compaction
  - HBase Regions
  - HBase Memstore

  More details on the tuning of OpenTSDB database is available in the documentation url:
  http://opentsdb.net/docs/build/html/user_guide/tuning.html
  
	

### YARN

### YARN Resource Manager UI - Job monitoring
<a name="yarn">

There are 2 web GUI for YARN:
	- YARN Resource Manager: http://quickstart.cloudera:8088/cluster
	- YARN Node Manager:  http://quickstart.cloudera:8042/node

On the web GUI for Resource Manager,

	- 1) Click on the Cluster section. This shows the cluster ID, Resource Manager State, Resource Manager High Availability State
	- 2) When a (mapreduce) job is executed, the job can be viewed by clicking "application" section -> "Jobs" section. You can view the job ID, map state, reduce state and overall state if it is running.
	- 3) You can view all the completed jobs by clicking "cluster" section -> "application" section -> "FINISHED" section. 
	- 4) You can also monitor jobs with other status: killed, failed, running, accepted. 
	- 5) You can also monitor scheduler by clicking on "cluster" section -> "scheduler" section. This shows you the details on used, available capacity and queue status.


The following URL provides documentation on job monitoring on YARN Resource Manager:
https://hadooptutorial.info/yarn-web-ui/

### Finding log files in YARN
<a name="yarnlog">
	
From this Web GUI, we can access the log files, by clicking on the tools section and then the local logs link. We can access the following logs:
	
| Type of log | name | location |
| :---: | :---: | :---: |
| node manager | yarn-yarn-nodemanager-quickstart.cloudera.log | var/log/hadoop-yarn/ | 
| proxy server | yarn-yarn-proxyserver-quickstart.cloudera.log | var/log/hadoop-yarn/ | 
| resource manager | yarn-yarn-resourcemanager-quickstart.cloudera.log | var/log/hadoop-yarn/ | 


The following URL provides documentation on job monitoring on YARN Resource Manager:
https://docs.cloudera.com/documentation/enterprise/5-13-x/topics/cm_dg_yarn_applications.html#concept_vh1_jtj_gk


### Manage services on YARN via CLI
<a name="yarncli">

**To start, stop, restart YARN** 

```
sudo service hadoop-yarn-resourcemanager start
sudo service hadoop-yarn-nodemanager start
sudo service hadoop-yarn-proxyserver start
```


**Deploy a service**

```
yarn app -launch ${SERVICE_NAME} ${PATH_TO_SERVICE_DEF_FILE}
yarn app -launch sleeper-service /path/to/local/sleeper.json
```

Params: - SERVICE_NAME: The name of the service. Note that this needs to be unique across running services for the current user. - PATH_TO_SERVICE_DEF: The path to the service definition file in JSON format.


**Flex a component of a service**

Increase or decrease the number of containers for a component.

```
yarn app -flex ${SERVICE_NAME} -component ${COMPONENT_NAME} ${NUMBER_OF_CONTAINERS}
yarn app -flex sleeper-service -component sleeper 2
# Set the sleeper component to 2 containers (absolute number).
```

**Stop a service**

Stopping a service will stop all containers of the service and the ApplicationMaster, but does not delete the state of a service, such as the service root folder on hdfs.

```
yarn app -stop ${SERVICE_NAME}
```

**Restart a stopped service**

Restarting a stopped service is easy - just call start!

```
yarn app -start ${SERVICE_NAME}
```

**Destroy a service**

In addition to stopping a service, it also deletes the service root folder on hdfs and the records in YARN Service Registry.

```
yarn app -destroy ${SERVICE_NAME}
```


### Flink
<a name="flink">

The following URL provides documentation on monitoring of jobs using Flink Web Dashboard:
https://flink.apache.org/news/2019/02/25/monitoring-best-practices.html


### Phoenix
### Monitoring Metrics in Phoenix
<a name="phoenix">

Phoenix has various metrics that provide an insight into what is going on within the Phoenix client as it is executing various SQL statements. These metrics are collected within the client JVM in two ways:
	- Request level metrics - collected at an individual SQL statement level
	- Global metrics - collected at the client JVM level

Request level metrics are helpful for figuring out at a more granular level about the amount of work done by every SQL statement executed by Phoenix. These metrics can be classified into three categories:

	- Mutation Metrics
	- Scan Task Metrics
	- Overall Query Metrics

More details on the metrics in Phoenix can be found in the following URL:
http://phoenix.apache.org/metrics.html



### Basic Tasks
### Start, Stop, Restart Service
<a name="restart">

To list all the services, use the following command:

```
ls /etc/init.d
```

```
sudo service <name of service> start
sudo service <name of service> stop
sudo service <name of service> restart
```
