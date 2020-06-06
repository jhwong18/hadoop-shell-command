## Hadoop shell commands (HDFS)
Useful Hadoop Shell Commands for HDFS

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
du (Development Unit) changes the group of the folder in HDFS
df (Disk Free) changes the owner of the folder in HDFS
```
hadoop fs -du <folder in HDFS>
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
```

| Options | Description |
| :---: | :---: | 
| <path> |	start checking from the path specified here |
| -move	| It moves a corrupted file to the lost+found directory. |
| -delete |	It deletes the corrupted files present in HDFS. |
| -openforwrite |	It prints the files which are opened for write |
| -files |	It prints the files being checked. |
| -blocks |	It prints out all the blocks of the file while checking. |
| -locations |	It prints the location of all the blocks of files while checking. |
| -racks |	It displays the network topology for DataNode locations. |
