## Example 1: Create dir in hadoop and example file on local system. Copy the file from the local system into the hdfs and display the file's size. 
```
[cloudera@quickstart ~]$ hadoop fs -ls /user/cloudera
```
Create directory
```
[cloudera@quickstart ~]$ hadoop fs -mkdir /user/cloudera/example 
```
Check if dir was created
```
[cloudera@quickstart ~]$ hadoop fs -ls /user/cloudera/
Found 1 items
drwxr-xr-x   - cloudera cloudera          0 2016-01-11 01:38 /user/cloudera/example
```
Create sample.txt in local dir
```
[cloudera@quickstart ~]$ vim sample.txt
```
check, if txt was created sucessfully
```
[cloudera@quickstart ~]$ ls
cloudera-manager  Downloads                   kerberos  Public      workspace
cm_api.py         eclipse                     lib       sample.txt
Desktop           enterprise-deployment.json  Music     Templates
Documents         express-deployment.json     Pictures  Videos
```
copy sample.txt from local dir into hdfs 
```
[cloudera@quickstart ~]$ hadoop fs -copyFromLocal /home/cloudera/sample.txt /user/cloudera/example
```
check, if copying was successful
```
[cloudera@quickstart ~]$ hadoop fs -ls /user/cloudera/example
Found 1 items
-rw-r--r--   1 cloudera cloudera         49 2016-01-11 01:46 /user/cloudera/example/sample.txt
```
check size of sample.txt, -h means to show it in a "human-readable" way
```
[cloudera@quickstart ~]$ hadoop fs -du -h /user/cloudera/example
49  49  /user/cloudera/example/sample.txt
```
So, the size of sample.txt is 49 Bytes


##Example 2: Getting several different sample files and merge those in one file

Getting some example files
```
[cloudera@quickstart ~]$ wget http://www.gutenberg.org/cache/epub/1112/pg1112.txt
--2016-01-11 02:11:21--  http://www.gutenberg.org/cache/epub/1112/pg1112.txt
Resolving www.gutenberg.org... 152.19.134.47
Connecting to www.gutenberg.org|152.19.134.47|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 178983 (175K) [text/plain]
Saving to: “pg1112.txt”

100%[=======================================================================================================================>] 178,983      267K/s   in 0.7s    

2016-01-11 02:11:22 (267 KB/s) - “pg1112.txt” saved [178983/178983]

[cloudera@quickstart ~]$ wget http://www.gutenberg.org/cache/epub/1524/pg1524.txt
--2016-01-11 02:11:38--  http://www.gutenberg.org/cache/epub/1524/pg1524.txt
Resolving www.gutenberg.org... 152.19.134.47
Connecting to www.gutenberg.org|152.19.134.47|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 192944 (188K) [text/plain]
Saving to: “pg1524.txt”

100%[=======================================================================================================================>] 192,944      269K/s   in 0.7s    

2016-01-11 02:11:39 (269 KB/s) - “pg1524.txt” saved [192944/192944]

[cloudera@quickstart ~]$ wget http://www.gutenberg.org/cache/epub/2264/pg2264.txt
--2016-01-11 02:11:51--  http://www.gutenberg.org/cache/epub/2264/pg2264.txt
Resolving www.gutenberg.org... 152.19.134.47
Connecting to www.gutenberg.org|152.19.134.47|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 119848 (117K) [text/plain]
Saving to: “pg2264.txt”

100%[=======================================================================================================================>] 119,848      352K/s   in 0.3s    

2016-01-11 02:11:52 (352 KB/s) - “pg2264.txt” saved [119848/119848]
```
Copy downloaded files to hdfs
```
[cloudera@quickstart ~]$ hadoop fs -put /home/cloudera/pg1112.txt /home/cloudera/pg1524.txt /home/cloudera/pg2264.txt /user/cloudera/example
```
Check directory
```
[cloudera@quickstart ~]$ hadoop fs -ls /user/cloudera/example
Found 4 items
-rw-r--r--   1 cloudera cloudera     178983 2016-01-11 02:17 /user/cloudera/example/pg1112.txt
-rw-r--r--   1 cloudera cloudera     192944 2016-01-11 02:17 /user/cloudera/example/pg1524.txt
-rw-r--r--   1 cloudera cloudera     119848 2016-01-11 02:17 /user/cloudera/example/pg2264.txt
-rw-r--r--   1 cloudera cloudera         49 2016-01-11 01:46 /user/cloudera/example/sample.txt
```
Create merged File on local system 
```
[cloudera@quickstart ~]$ hadoop fs -getmerge /user/cloudera/example  ./mergedFile.txt
```
put file on hdfs
```
[cloudera@quickstart ~]$ hadoop fs -put /home/cloudera/mergedFile.txt /user/cloudera/example
```
check if file is there
```
[cloudera@quickstart ~]$ hadoop fs -ls /user/cloudera/example
Found 5 items
-rw-r--r--   1 cloudera cloudera     491824 2016-01-11 02:29 /user/cloudera/example/mergedFile.txt
-rw-r--r--   1 cloudera cloudera     178983 2016-01-11 02:17 /user/cloudera/example/pg1112.txt
-rw-r--r--   1 cloudera cloudera     192944 2016-01-11 02:17 /user/cloudera/example/pg1524.txt
-rw-r--r--   1 cloudera cloudera     119848 2016-01-11 02:17 /user/cloudera/example/pg2264.txt
-rw-r--r--   1 cloudera cloudera         49 2016-01-11 01:46 /user/cloudera/example/sample.txt
```
show file size
```
[cloudera@quickstart ~]$ hadoop fs -du -h  /user/cloudera/example/*
480.3 K  480.3 K  /user/cloudera/example/mergedFile.txt
174.8 K  174.8 K  /user/cloudera/example/pg1112.txt
188.4 K  188.4 K  /user/cloudera/example/pg1524.txt
117.0 K  117.0 K  /user/cloudera/example/pg2264.txt
49  49  /user/cloudera/example/sample.txt
```
the merged file is the sum of each of the .txt files and also a vim look into the merged file shows it was sucessful.

##Example 3: Creating Snapshots and compare those
check all snapshottable directories where the current user has permission to take snapshtos. 
```
[cloudera@quickstart ~]$ hdfs lsSnapshottableDir
#empty
```
make directory snapshottable
```
[cloudera@quickstart ~]$ hdfs dfsadmin -allowSnapshot /user/cloudera/example
Allowing snaphot on /user/cloudera/example succeeded
```
creating Snapshot
```
[cloudera@quickstart ~]$ hdfs dfs -createSnapshot /user/cloudera/example snapshot1
Created snapshot /user/cloudera/example/.snapshot/snapshot1
```
check snapshot contents
```
[cloudera@quickstart ~]$ hdfs dfs -ls /user/cloudera/example/.snapshot/snapshot1
Found 5 items
-rw-r--r--   1 cloudera cloudera     491824 2016-01-11 02:29 /user/cloudera/example/.snapshot/snapshot1/mergedFile.txt
-rw-r--r--   1 cloudera cloudera     178983 2016-01-11 02:17 /user/cloudera/example/.snapshot/snapshot1/pg1112.txt
-rw-r--r--   1 cloudera cloudera     192944 2016-01-11 02:17 /user/cloudera/example/.snapshot/snapshot1/pg1524.txt
-rw-r--r--   1 cloudera cloudera     119848 2016-01-11 02:17 /user/cloudera/example/.snapshot/snapshot1/pg2264.txt
-rw-r--r--   1 cloudera cloudera         49 2016-01-11 01:46 /user/cloudera/example/.snapshot/snapshot1/sample.txt
```
Changing the orgin directory
At first, download new file
```
[cloudera@quickstart ~]$ wget http://www.gutenberg.org/cache/epub/1342/pg1342.txt
--2016-01-13 00:32:26--  http://www.gutenberg.org/cache/epub/1342/pg1342.txt
Resolving www.gutenberg.org... 152.19.134.47
Connecting to www.gutenberg.org|152.19.134.47|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 717574 (701K) [text/plain]
Saving to: “pg1342.txt”
```
put on hdfs and check if it is in the directory
```
[cloudera@quickstart ~]$ hadoop fs -put pg1342.txt /user/cloudera/example
[cloudera@quickstart ~]$ hdfs dfs -ls /user/cloudera/example
Found 6 items
-rw-r--r--   1 cloudera cloudera     491824 2016-01-11 02:29 /user/cloudera/example/mergedFile.txt
-rw-r--r--   1 cloudera cloudera     178983 2016-01-11 02:17 /user/cloudera/example/pg1112.txt
-rw-r--r--   1 cloudera cloudera     717574 2016-01-13 00:34 /user/cloudera/example/pg1342.txt
-rw-r--r--   1 cloudera cloudera     192944 2016-01-11 02:17 /user/cloudera/example/pg1524.txt
-rw-r--r--   1 cloudera cloudera     119848 2016-01-11 02:17 /user/cloudera/example/pg2264.txt
-rw-r--r--   1 cloudera cloudera         49 2016-01-11 01:46 /user/cloudera/example/sample.txt
```
Create snapshot
```
[cloudera@quickstart ~]$ hdfs dfs -createSnapshot /user/cloudera/example snapshot2
Created snapshot /user/cloudera/example/.snapshot/snapshot2
```
Compare two snapshots
```
[cloudera@quickstart ~]$ hdfs snapshotDiff /user/cloudera/example snapshot1 snapshot2
Difference between snapshot snapshot1 and snapshot snapshot2 under directory /user/cloudera/example:
M	. ## the dir has been modified
+	./pg1342.txt ##the file has been added
```

delete Snapshot1 and verfiy deleting
```
[cloudera@quickstart ~]$ hdfs dfs -deleteSnapshot /user/cloudera/example snapshot1
[cloudera@quickstart ~]$ hdfs dfs -ls /user/cloudera/example/.snapshot
Found 1 items
drwxr-xr-x   - cloudera cloudera          0 2016-01-13 00:38 /user/cloudera/example/.snapshot/snapshot2
```
Delete "important file" 
entire dir cannot be deleted, because it is snapshottable: 
```
[cloudera@quickstart ~]$ sudo -u hdfs hadoop fs -rm -r -skipTrash /user/cloudera/example
rm: The directory /user/cloudera/example cannot be deleted since /user/cloudera/example is snapshottable and already has snapshots
```
delete sample.txt
```
[cloudera@quickstart ~]$ hdfs dfs -rm -r /user/cloudera/example/sample.txt
16/01/13 01:08:10 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.
Deleted /user/cloudera/example/sample.txt
```
Recover file 
The removed sample.txt file is contained in the snapshot2
```
[cloudera@quickstart ~]$ hdfs dfs -cat /user/cloudera/example/.snapshot/snapshot2/sample.txt
Hello, I am the example for the Big Data Projekt
```
Copying the file from the snapshot in the origin dir
```
[cloudera@quickstart ~]$ hdfs dfs -cp /user/cloudera/example/.snapshot/snapshot2/sample.txt /user/cloudera/example
```
The file is recovered sucessfully:
```
[cloudera@quickstart ~]$ hdfs dfs -cat /user/cloudera/example/sample.txt
Hello, I am the example for the Big Data Projekt
```

























