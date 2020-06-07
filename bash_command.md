## Unix/Linux Commands
Simple useful Linx/Unix Commands on Bash Terminal.

### Table of Contents
- [ls](#ls)
- [cd](#cd)
- [pwd](#pwd)
- [du, df](#du)
- [man](#man)
- [mkdir](#mkdir)
- [rm, mv, cp](#rm)
- [touch](#touch)
- [cat, head, tail](#cat)
- [more, less](#more)
- [wc](#wc)
- [grep](#grep)
- [vim](#vim)
- [cut](#cut)
- [tr](#tr)
- [sort](#sort)
- [free](#free)
- [top](#top)
- [ps, kill](#ps)

#### Operators 
- [operators](#operators)



### -ls
<a name="ls">
To list all the folders or files in the directory. -lsh: long listing format, sort by size, remove hash. -l: long listing format.
  
```
-ls -lSh
-ls
-ls -l
-ls -lh
```

### -cd
<a name="cd">
Change directory
  
```
cd 
```

### -pwd
<a name="pwd">
Print current Working Directory.
  
```
pwd 
```

### -du, -df
<a name="du">
du (Disk Usage) prints out the summary of disk usage in the folder.
df (Disk Free) prints out the summary of disk free space in the folder.
```
du
df
du -h
df -h
```

### -man
<a name="man">
manual / help function
  
```
man <command>
```
  
### -mkdir
<a name="mkdir">
To create a directory in file system 
  
```
mkdir <path/dir> 
```


### -rm, -mv, -cp
<a name="rm">
rm helps to remove the file 
mv helps to move the file to from another folder. Also able to rename a file
cp helps to copy the file into another folder
-r: recursively apply the command to all files in the directory
*.txt: removes all file in .txt format in the directory
  
```
rm <file name>
rm -r <file name>
rm ~/Downloads/*.txt
mv <file name> <dest folder name>
mv <current file name> <new file name in the same folder>
mv -r <file name> <dest folder name>
cp <file name> <dest folder name>
cp -r <file name> <dest folder name>

```

### -touch
<a name="touch">

touch creates an empty file of 0 bytes

```
touch <file name>
```

### -cat, -head, -tail
<a name="cat">
cat helps to print the contents of a file
head helps to print the first n lines of a file
tail helps to print the last n lines of a file

```
cat <file name 1> <file name 2> <file name 3>
head <file name 1> <file name 2> <file name 3>
head -5 <file name 1> <file name 2> <file name 3>
tail <file name 1> <file name 2> <file name 3>
```


### -more, -less
<a name="more">
more helps to print the contents of a file, one text screen at a time
less helps to print the contents of a file, one text screen at a time

```
more <file name 1> <file name 2> <file name 3>
less <file name 1> <file name 2> <file name 3>
```

### -wc
<a name="wc">
wc helps to print the word count contents of a file
-c: number of characters
-l: number of lines
  
```
wc <'pattern'> <file name> 
wc 'this' filename.txt
```


### -grep
<a name="grep">
wc helps to find any line that matches a specified patter in the file
-c: number of characters
-l: number of lines
  
```
wc <file name 1> <file name 2> <file name 3>
wc -c <file name 1> <file name 2> <file name 3>
wc -l <file name 1> <file name 2> <file name 3>
```


### -vim
<a name="vim">
to open vim editor. vim can be used to create shell scripts or py scripts.
    
```
vim <file name>
vim shell_script.sh
vim python_script.py
```
In vim editor, enter esc and :wq to write and save a file


### -cut
<a name="cut">
cut helps to print out a subset of each line of a file 
  
```
cut -d -f <file name>
cut -d ' ' -f1 file.txt
```


### -tr
<a name="tr">
translates one set of characters into the other 
| is used to run the outputs of the previous command into the subsequent command 
-d: drop the set of characters
  
```
tr <original set of characters> <new set of characters for replacement>
tr -d <original set of characters>
tr ' ' '.'
tr -d '.'
cat file_name.txt | tr ' ' '.'
```

### -sort
<a name="sort">
sort	Sort the contents of a text file.
sort -r	Sort the output in the reverse order. Reverse means - to reverse the result of comparsions
sort -k	-k or --key=POS1[,POS2] Start a key at POS1 (origin 1), end it at POS2 (default end of the line) (ex.: “sort -k2,2 multilined_file.txt”).
sort -n	Compare according to string numerical value.

```
sort -k2,2 -r file.txt
```



### -awk
<a name="awk">
awk	"Aho, Weinberger and Kernigan", Bell Labs, 1970s. Interpreted programming language for text processing.

```
awk -F	(see above) + Set the field separator
```


### operators
<a name="operators">
operators in bash: |, ||, &, &&, >, >>
  
| Operators | Description |
| :---: | :---: | 
| > |	overwrites the file if it exists or create the file if it doesnt |
| >>	| appends to the file or create the file if it doesnt |
| '\|' |	It deletes the corrupted files present in HDFS. |
| '\|\|' |	Execute the second command only if the first command fails |
| & |	Make the command run in the background |
| && |	Execute the second command only if the first command succeeds |


### -free
<a name="free">
finds out the amount of free space

```
free
```

### -top
<a name="top">
gives a real time dynamic view of the system

```
top
```
  

### -ps, -kill
<a name="ps">
provides a snapshot of the current processes in the system
kill a process
  
```
ps
kill
```
