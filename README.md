## Hadoop shell commands (HDFS)
Useful Hadoop Shell Commands for HDFS. To refer to any file system use:
```
hadoop fs
```

To refer to hadoop distributed file system, use:
```
hdfs dfs 
```

### Table of Contents
- [version](#version)
- [mkdir](#mkdir)
- [ls](#ls)
- [copyToLocal, copyFromLocal](#local)
- [rm, mv, cp](#rm)
- [get, put](#get)
- [cat](#cat)
- [moveToLocal, moveFromLocal](#movelocal)
- [tail](#tail)
- [expunge, setrep](#setrep)
- [chgrp, chown](#ch)
- [du, df](#du)
- [fsck](#fsck)
- [touchz](#touchz)
- [test, text, stat](#stat)
- [usage, help](#help)
- [chmod](#chmod)
- [appendToFile](#append)
- [count](#count)
- [checksum, find, getmerge](#getmerge)

#### HDFS Admin Commands
- [dfsadmin](#admin)
- [datanode](#datanode)
- [namenode](#secondarynamenode)
- [balancer](#balancer)


### -version
<a name="version">
To get the hadoop version
  
```
hadoop fs -version
```

### -mkdir
<a name="mkdir">
To create a directory in HDFS 
  
```
hadoop fs -mkdir <path/dir> 
```

### -ls
<a name="ls">
To list all the folders or files in HDFS. -R to list the files recursively across different folders. 
  
```
hadoop fs -ls 
hadoop fs -ls /user/cloudera
hadoop fs -ls /
hadoop fs -ls -R
hadoop fs -ls -R /folder_name
```

### -copyToLocal, -copyFromLocal
<a name="local">
copyToLocal helps to copy the file from local system into HDFS
copyFromLocal helps to copy the file from HDFS to local system
  
```
hadoop fs -copyToLocal <src in local system> <dest in HDFS> 
hadoop fs -copyToLocal /Downloads/file.txt /hdfs_folder
hadoop fs -copyFromLocal <src in HDFS> <dest in local system> 
hadoop fs -copyFromLocal /hdfs_folder/part-r-00000 /Downloads/out.txt
```


### -rm, -mv, -cp
<a name="rm">
rm helps to remove the file from HDFS
mv helps to remove the file from HDFS into another folder within HDFS
cp helps to copy the file from HDFS into another folder within HDFS 
  
```
hadoop fs -rm <file in HDFS>
hadoop fs -rm /hdfs_folder/file.txt
hadoop fs -mv <file in HDFS> <folder in HDFS>
hadoop fs -mv /folder/file.txt /folder2
hadoop fs -cp <file in HDFS> <folder in HDFS>

```

### -get, -put
<a name="get">
get helps to get the file from HDFS into local system
put helps to put the file from HDFS into local system
  
```
hadoop fs -get <file in HDFS> <folder in local system>
hadoop fs -put <file in local system> <folder in HDFS>
```


### -cat
<a name="cat">
cat helps to print the contents of a file
  
```
hadoop fs -cat <file in HDFS>
hadoop fs -cat out/part-r-00000
```

### -moveToLocal, -moveFromLocal
<a name="movelocal">
moveToLocal helps to move the contents from HDFS to a local file system. Files are moved, not copied.
moveFromLocal helps to move the contents from local file system to HDFS. Files are moved, not copied.
  
```
hadoop fs -moveToLocal <file in HDFS> <folder in local system>
hadoop fs -moveToLocal /folder/part-r-0000 /Downloads/
hadoop fs -moveFromLocal <file in local system> <folder in HDFS>
hadoop fs -moveFromLocal ~/Downloads/alice.txt
```


### -tail
<a name="tail">
shows the last 1kb of the file
  
```
hadoop fs -tail <file in HDFS>
hadoop fs -tail alice.txt
```

### -expunge, -setrep
<a name="setrep">
expunge deletes the trash bin of the HDFS
setrep determines the number of replications for writing of files in HDFS
  
```
hadoop fs -expunge
hadoop fs -setrep <# of rep> <folder in HDFS>
hadoop fs -setrep 3 /folder
```

### -chgrp, -chown
<a name="ch">
chgrp changes the group of the folder in HDFS
chown changes the owner of the folder in HDFS
```
hadoop fs -chgrp <new name of group> <folder in HDFS>
hadoop fs -chown <owner name> <folder in HDFS>
```
  
### -du, -df
<a name="du">
du (Disk Usage) changes the group of the folder in HDFS. -s prints out the summary of disk usage in the folder.
df (Disk Free) changes the owner of the folder in HDFS
```
hadoop fs -du -s <folder in HDFS>
hadoop fs -du
hadoop fs -du /folder 
hadoop fs -df <folder in HDFS>
hadoop fs -df
hadoop fs -df /folder
```
 
### -fsck
<a name="fsck">

fsck (File System Check) check the health of the files in directory present in HDFS

```
hdfs fsck <folder in HDFS> [ -move | -delete | -openforwrite] [-files [-blocks [-locations | -racks]]]
hdfs fsck -files 
```

| Options | Description |
| :---: | :---: | 
| path |	start checking from the path specified here |
| -move	| It moves a corrupted file to the lost+found directory. |
| -delete |	It deletes the corrupted files present in HDFS. |
| -openforwrite |	It prints the files which are opened for write |
| -files |	It prints the files being checked. Only checks the files instead of the blocks. |
| -blocks |	It prints out all the blocks of the file while checking. |
| -locations |	It prints the location of all the blocks of files while checking. |
| -racks |	It displays the network topology for DataNode locations. |



### -touchz
<a name="touchz">

touchz creates an empty file of 0 bytes in HDFS

```
hadoop -fs -touchz <file name in HDFS>
hadoop -fs -touchz /folder/file.txt
hadoop -fs -touchz /folder/file
```


### -test, -text, -stat
<a name="stat">

test is used to test an HDFS file’s existence of zero length of the file or whether if it is a directory or not.
text is used to display the zip file in text format. The allowed formats are zip and TextRecordInputStream.
stat is used to print the information about file ‘test’ present in the dataflair directory of HDFS.

```
hadoop -fs -test -[defsz] <file name in HDFS>
hadoop -fs -text <file name in HDFS>
hadoop -fs -stat  %b /folder/file
```

| Options for -test| Description |
| :---: | :---: | 
|–d	| used to check whether if it is a directory or not, returns 0 if it is a directory |
|–e |	used to check whether they exist or not, returns 0 if the exists|
|–f |	used to check whether there is a file or not, returns 0 if the file exists|
|–s |	used to check whether the file size is greater than 0 bytes or not, returns 0 if the size is greater than 0 bytes|
|–z |	used to check whether the file size is zero bytes or not. If the file size is zero bytes, then returns 0 or else returns |

| Options for -stat | Description |
| :---: | :---: | 
|%b | file size in bytes|
|%g | group name of owner|
|%n | file name|
|%o |  block size|
|%r  |  replication|
|%u | user name of owner|
|%y | modification date|


### -usage, -help
<a name="help">

usage/help returns the help for an individual command.

```
hadoop -fs -help <command>
hadoop -fs -usage <command>
```

### -chmod
<a name="chmod">

change permissions/mode of the file in HDFS 

```
hadoop -fs -chmod <file name in HDFS>
```


### -appendToFile
<a name="append">

appendToFile appends the localfile1, localfile2 present in the local filesystem into the a specified file on the HDFS filesystem.

```
hadoop fs -appendToFile <localfile1> <localfile2> <localfile3> <destination HDFS folder>
```

### -count
<a name="count">

count counts the number of files, directories, and bytes under the paths that matches the specified file pattern.

```
hadoop fs -count [options] <HDFS folder>
```

Options:
-q –  shows quotas(quota is the hard limit on the number of names and amount of space used for individual directories)
-u  –  it limits output to show quotas and usage only
-h  –  shows sizes in a human-readable format
-v  –  shows header line

### -getmerge, -checksum, -find
<a name="getmerge">

```
hadoop fs -getmerge <src> <localdest>
hadoop fs -find <path> … <expression>
hadoop fs -checksum <src>
```

### dfsadmin
<a name="admin">
dfsadmin -report gives the report on the HDFS cluster
dfsadmin -report -files
dfsadmin -report -refreshNodes helps to decommission and commission of nodes 
  
```
hdfs dfsadmin
hdfs dfsadmin -report
hdfs dfsadmin -report -files
hdfs dfsadmin -report -refreshNodes 
```

### balancer
<a name="balancer">
balancer helps to re-organize the number of blocks that each datanode should run on. It should be used when a new node is added or an existing node is decommissioned.  
  
```
hdfs balancer [-threshold <threshold>]
hdfs balancer -threshold 20
```

### datanode
<a name="datanode">
Helps to restart the datanodes in HDFS. -rollback to return to the previous version.
  
```
hdfs datanode
hdfs datanode -rollback
```

### secondarynamenode
<a name="secondarynamenode">
To run the secondary NameNode. 
  
```
hdfs secondarynamenode
```
| Options | Description |
| :---: | :---: | 
|-checkpoint|	a checkpoint on the secondary NameNode is performed if the size of the EditLog is greater than or equal to fs.checkpoint.size|
|-force	| a checkpoint is performed regardless of the EditLog size;|
|–geteditsize|	EditLog size is displayed|
