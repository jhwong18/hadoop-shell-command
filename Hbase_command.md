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
- [add_peer](#clust)
- [remove_peer](#clust)
- [start_replication](#clust)
- [stop_replication](#clust)


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

#### Drop, Drop_all
<a name="Drop">
Drop:
  - To delete the table present in HBase, first we have to disable it
  - To drop the table present in HBase, first we have to disable it
  - Before execution of this command, it is necessary that you disable table "education."
  
Drop_all:
  - This command will drop all the tables matching the given regex
  - Tables have to disable first before executing this command using disable_all
  - Tables with regex matching expressions are going to drop from HBase

```
drop <tablename1> <tablename2>
drop_all <"matching regex expression">
```

#### Enable
<a name="Enable">
Is_enabled:
  - This command will verify whether the named table is enabled or not. Usually, there is a little confusion between "enable" and "is_enabled" command action, which we clear here

Suppose a table is disabled, to use that table we have to enable it by using enable command
is_enabled command will check either the table is enabled or not

```
is_enabled <tablename1> <tablename2>
```


#### Alter
<a name="Alter">
  
This command alters the column family schema.
ALter command is able to:
  - Altering single, multiple column family names
  - Deleting column family names from table
  - Several other operations using scope attributes with table

```
alter <tablename>, NAME=><column familyname>, VERSIONS=>5
```

To change or add the 'guru99_1' column family in table 'education' from current value to keep a maximum of 5 cell VERSIONS, "education" is table name created with column name "guru99" previously.
Here with the help of an alter command we are trying to change the column family schema to guru99_1 from guru99

```
alter 'education', NAME='guru99_1', VERSIONS=>5
```

You can also operate the alter command on several column families as well. For example, we will define two new column to our existing table "education". We can change more than one column schemas at a time using this command
guru99_2 and guru99_3 as shown in above screenshot are the two new column names that we have defined for the table education
We can see the way of using this command in the previous screen shot

```
alter 'edu', 'guru99_1', {NAME => 'guru99_2', IN_MEMORY => true}, {NAME => 'guru99_3', VERSIONS => 5}
```

delete column family from the table. To delete the 'f1' column family in table 'education'.

```
alter 'education', NAME => 'f1', METHOD => 'delete'
alter 'education', 'delete' =>' guru99_1'    

```

#### Alter_status
<a name="Alter">
  
Through this command, you can get the status of the alter command, which indicates the number of regions of the table that have received the updated schema pass table name.

```
alter_status <tablename>
```


- [Put](#Put)
- [Get](#Get)
- [Delete, Delete all](#Delete)
- [Truncate](#Truncate)
- [Scan](#Scan)

#### Data manipulation commands
#### Count
<a name="Count">
  - The command will retrieve the count of a number of rows in a table. The value returned by this one is the number of rows.
  - Current count is shown per every 1000 rows by default.
  - Count interval may be optionally specified.
  - Default cache size is 10 rows.
  - Count command will work fast when it is configured with right Cache. 


```
count <'tablename'>, CACHE =>1000
count 'guru99', CACHE=>1000
count 'guru99', INTERVAL => 100000
count 'guru99', INTERVAL =>10, CACHE=> 1000
```

If suppose if the table "Guru99" having some table reference like say g.

We can run the count command on table reference also like below

```
g.count INTERVAL=>100000
g.count INTERVAL=>10, CACHE=>1000
```

#### Put
<a name="Put">
It will put a cell 'value' at defined or specified table or row or column.
It will optionally coordinate time stamp.

- Here we are placing values into table "guru99" under row r1 and column c1

```
put <'tablename'>,<'rowname'>,<'columnvalue'>,<'value'>
put 'guru99', 'r1', 'c1', 'value', 10
g.put 'guru99', 'r1', 'c1', 'value', 10

```


#### Get
<a name="Get">
Here <Additional Parameters> include:
  - TIMERANGE
  - TIMESTAMP
  - VERSIONS
  - FILTERS.

By using this command, you will get a row or cell contents present in the table. In addition to that you can also add additional parameters to it like TIMESTAMP, TIMERANGE,VERSIONS, FILTERS, etc. to get a particular row or cell content.

- For table "guru99' row r1 and column c1 values will display using this command as shown in the above screen shot
- For table "guru99"row r1 values will be displayed using this command
- For table "guru99"row 1 values in the time range ts1 and ts2 will be displayed using this command
- For table "guru99" row r1 and column families' c1, c2, c3 values will be displayed using this command

```
get <'tablename'>, <'rowname'>, {< Additional parameters>}
get 'guru99', 'r1', {COLUMN => 'c1'}
get 'guru99', 'r1'
get 'guru99', 'r1', {TIMERANGE => [ts1, ts2]}
get 'guru99', 'r1', {COLUMN => ['c1', 'c2', 'c3']}
```

#### Delete, Delete_all
<a name="Delete">
Delete:
- This command will delete cell value at defined table of row or column.
- Delete must and should match the deleted cells coordinates exactly.
- When scanning, delete cell suppresses older versions of values.
- The above execution will delete row r1 from column family c1 in table "guru99."
- Suppose if the table "guru99" having some table reference like say g.
- We can run the command on table reference also like hbase> g.delete 'guru99', 'r1', 'c1'".
  
Delete_all:
  - This Command will delete all cells in a given row.
  - We can define optionally column names and time stamp to the syntax.


```
delete <'tablename'>,<'row name'>,<'column name'>
delete 'guru99', 'r1', 'c1''. 
deleteall <'tablename'>, <'rowname'>
deleteall 'guru99', 'r1', 'c1'
```


#### Truncate
<a name="Truncate">
After truncate of an hbase table, the schema will present but not the records. This command performs 3 functions; those are listed    below:
  - Disables table if it already presents
  - Drops table if it already presents
  - Recreates the mentioned table

```
truncate <tablename>
```



#### scan
<a name="scan">
This command scans entire table and displays the table contents.

- We can pass several optional specifications to this scan command to get more information about the tables present in the system.
- Scanner specifications may include one or more of the following attributes.
- These are TIMERANGE, FILTER, TIMESTAMP, LIMIT, MAXLENGTH, COLUMNS, CACHE, STARTROW and STOPROW.
```
scan <'tablename'>, {Optional parameters}
scan 'guru99' 
```

The different usages of scan command


| Command | Usage |
| :---: | :---: | 
| scan '.META.', {COLUMNS => 'info:regioninfo'} | It display all the meta data information related to columns that are present in the tables in HBase |
| scan 'guru99', {COLUMNS => ['c1', 'c2'], LIMIT => 10, STARTROW => 'xyz'} | It display contents of table guru99 with their column families c1 and c2 limiting the values to 10 |
| scan 'guru99', {COLUMNS => 'c1', TIMERANGE => [1303668804, 1303668904]} | It display contents of guru99 with its column name c1 with the values present in between the mentioned time range attribute value |
| scan 'guru99', {RAW => true, VERSIONS =>10} |  In this command RAW=> true provides advanced feature like to display all the cell values present in the table guru99 |


#### Cluster Replication Commands
<a name="clust">
  
These commands work on cluster set up mode of HBase.
For adding and removing peers to cluster and to start and stop replication these commands are used in general.

| Command | Functionality | Example |
| :---: | :---: | :---: | 
| add_peer | Add peers to cluster to replicate | hbase> add_peer '3', zk1,zk2,zk3:2182:/hbase-prod |
| remove_peer | Stops the defined replication stream. Deletes all the metadata information about the peer | hbase> remove_peer '1' |
| start_replication | Restarts all the replication features | hbase> start_replication |
| stop_replication | Stops all the replication features | hbase>stop_replication |
