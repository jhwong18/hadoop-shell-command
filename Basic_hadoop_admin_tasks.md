## Hadoop Administrative tasks
Basic Hadoop Administrative tasks and the corresponding commands.

### Table of Contents
#### Hadoop Tasks

##### Memory
- [Maintain Disk free space in OS Space (Logs space and deleting old logs)](#OSspace)
- [CPU Utilization](#cpu)
- [Memory Utilization](#memory)
- [Low Swap Space](#swap)

##### Hadoop
- [Monitor Cluster Health in Hadoop](#hadoop)
- [Handle Alerts & Warnings in Ambari](#alerts)
- [Monitor Namenode UI](#namenode)

##### Hbase
- [Monitor Hbase Master UI](#hbase)
- [Region Servers Health check](#regionservers)

##### OpenTSDB
- [Monitor Stats and Data loading in OpenTSDB](#opentsdb)
- [Finding log files in OpenTSDB](#tsdblog)
- [Check Connection between OpenTSDB and Hbase](#tsdbconnection)


##### YARN
- [Yarn Resource Manager UI - Job monitoring](#yarn)

##### Flink
- [Flink Dashboard - Flink jobs monitoring](#flink)

##### HDFS
- [HDFS disk space alert](#hdfs)


#### Basic Tasks
- [Start, Stop, Restart Service](#restart)
- [Log files location](#log)
- [Get Admin report](#admin)




### Maintain disk free space in OS Space (Logs space and deleting old logs)
<a name="OSspace">
With the usage of Hadoop platforms and services, various issues regarding lack of memory space may occur in the servers:
- Logical Disk Free space is too low
- Swap memory (space) is too low
- CPU Utilization is too high
- Used memory is too high (Low RAM available)
  
Logical Disk Free Space is too low is due to the lack of free space in either of the following folders:
  - /opt
  - /tmp
  - /var/log
  - /var/lib


| Directory in /opt | Description |
| :---: | :---: |
| /opt |	/opt is reserved for the installation of add-on application software packages | 


| Directory in /tmp | Description |
| :---: | :---: |
| /tmp |	The /tmp directory must be made available for programs that require temporary files. Programs must not assume that any files or directories in /tmp are preserved between invocations of the program. | 


| Directory in /var | Description |
| :---: | :---: |
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
  free -h
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
  
  
### Hbase
### Monitor Hbase Master UI
<a name="hbase">

#### To start, stop, restart services

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
- [Region Servers Health check](#regionservers)

