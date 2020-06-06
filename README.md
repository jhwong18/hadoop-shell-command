## Hadoop shell commands (HDFS)
Useful Hadoop Shell Commands for HDFS

### Table of Contents
- [version](#version)
- [mkdir](#mkdir)
- [ls](#ls)
- [copyToLocal, copyFromLocal](#local)
- [rm, mv, cp](#rm)
- [get, put](#get)
- [cat]#(cat)

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
