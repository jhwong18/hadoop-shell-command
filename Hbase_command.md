## Hbase shell commands
Useful Hbase Shell Commands for Database Definition Language (DDL), Database Modification Language (DML), General and Cluster Management Commands. 

```
hbase Shell
hbase(main):001:0>
```

### Table of Contents
#### General Commands
- [status](#status)
- [version](#version)
- [table_help (scan, drop, get, put, disable, etc.)](#table_help)
- [whoami](#whoami)


#### Data Definition commands
- [Create](#Create)
- [List](#List)
- [Describe](#Describe)
- [Disable, Disable_all](#Disable)
- [Enable](#Enable)
- [Drop, Drop_all](#Drop)
- [Show_filters](#Show_filters)
- [Alter, Alter_status](#Alter)


#### Data manipulation commands
- [Count](#Count)
- [Put](#Put)
- [Get](#Get)
- [Delete, Delete all](#Delete)
- [Truncate](#Truncate)
- [Scan](#Scan)


#### Cluster Replication Commands
- [add_peer](#add_peer)
- [remove_peer](#remove_peer)
- [start_replication](#start_replication)
- [stop_replication](#stop_replication)


#### General commands
#### status
<a name="status">
status command will give details about the system status like a number of servers present in the cluster, active server count, and average load value. The parameters can be 'summary', 'simple', or 'detailed', the default parameter provided is "summary".
  
```
status
status 'simple'
status 'summary'
status 'detailed'
```

#### version
<a name="version">
version command will display the currently used HBase version in command mode

```
version
```

#### table_help
<a name="table_help">
table_help command will show:
  - What and how to use table-referenced commands
  - It will provide different HBase shell command usages and its syntaxes
  - It will give table manipulations commands like put, get and all other commands information.
  
```
table_help
```

#### whoami
<a name="whoami">
This command "whoami" is used to return the current HBase user information from the HBase cluster.
It will provide information like:
  - Groups present in HBase
  - The user name 
  
```
whoami
```

#### Data Definition commands
#### Create
<a name="Create">
Create command creates a table in HBase with the specified name given according to the dictionary or specifications as per column family.
  
```
create <tablename>, <columnfamilyname1>, <columnfamilyname2>, <columnfamilyname3>
create 'notifications', 'attributes', 'metrics'
```


#### List
<a name="List">
"List" command will display:
  - all the tables that are present or created in HBase
  - The output showing in above screen shot is currently showing the existing tables in HBase
  - We can filter output values from tables by passing optional regular expression parameters

```
list
```

#### Describe
<a name="Describe">
"Describe" command describes the named table:
  - It will give more information about column families present in the mentioned table
  - In our case, it gives the description about table "education."
  - It will give information about table name with column families, associated filters, versions and some more details.

```
describe <tablename>
```


#### Disable, Disable_all
<a name="Disable">
Disable:
  - This command will start disabling the named table
  - If table needs to be deleted or dropped, it has to disable first
  
Disable_all:
  - This command will disable all the tables matching the given regex.
  - The implementation is same as delete command (Except adding regex for matching)
  - Once the table gets disable the user can able to delete the table from HBase
  - Before delete or dropping table, it should be disabled first

```
disable <tablename>
disable_all <"matching regex">
```


#### Enable
<a name="Enable">
Enable:
  - This command will start enabling the named table
  - Whichever table is disabled, to retrieve back to its previous state we use this command
  - If a table is disabled in the first instance and not deleted or dropped, and if we want to re-use the disabled table then we have to enable it by using this command.
 

```
enable <tablename>
```

#### Show_filters
<a name="Show_filters">
This command displays all the filters present in HBase like ColumnPrefix Filter, TimestampsFilter, PageFilter, FamilyFilter, etc.
```
show_filters
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
