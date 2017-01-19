# Uninstalling HDP on Linux

### Stopping services using command line

`curl -u ambari_USERNAME:ambari_PASSWORD -H “X-Requested-By: ambari” -X PUT -d ‘{“RequestInfo”:{“context”:”Stop Service”},”Body”:{“ServiceInfo”:{“state”:”INSTALLED”}}}’ http://AMBARI_SERVER_HOST:8080/api/v1/clusters/CLUSTER_NAME/services/SERVICE_NAME`

Example:
curl -u admin:admin -H “X-Requested-By: ambari” -X PUT -d ‘{“RequestInfo”:{“context”:”Stop Service”},”Body”:{“ServiceInfo”:{“state”:”INSTALLED”}}}’ http://localhost:8080/api/v1/clusters/hdp_test/services/ZOOKEEPER_SERVER

Few of the components names as listed [here](http://henning.kropponline.de/2015/06/07/services-and-state-with-ambari-rest-api/)


### Deleting cluster

`curl -u admin:admin -H "X-Requested-By: ambari" -X DELETE http://localhost:8080/api/v1/clusters/hdp_test`


### Remove and clean hadoop packages completely

```
yum -y remove hive\*
yum -y remove oozie\*
yum -y remove pig\*
yum -y remove zookeeper\*
yum -y remove tez\*
yum -y remove hbase\*
yum -y remove ranger\*
yum -y remove knox\*
yum -y remove ranger\*
yum -y remove storm\*
yum -y remove hadoop\*
```

### Remove ambari server

```
ambari-server stop
yum -y erase ambari-server
```

### Remove ambari-agent

```
ambari-agent stop
yum -y erase ambari-agent
```


### Remove log directory 

```
/var/log/ambari-metrics-monitor
/var/log/hadoop
/var/log/hbase
/var/log/hadoop-yarn
/var/log/hadoop-mapreduce
/var/log/hive
/var/log/oozie
/var/log/zookeeper
/var/log/flume
/var/log/hive-hcatalog
/var/log/falcon
/var/log/knox
/var/lib/hive
/var/lib/oozie
```

### Remove hadoop directory

```
/usr/hdp
/usr/bin/hadoop
/tmp/hadoop
/var/hadoop
/hadoop/*
/local/opt/hadoop
```

### Remove all config directories

```
/etc/hadoop
/etc/hbase
/etc/oozie
/etc/phoenix
/etc/hive
/etc/zookeeper
/etc/flume
/etc/hive-hcatalog
/etc/tez
/etc/falcon
/etc/knox
/etc/hive-webhcat
/etc/mahout
/etc/pig
/etc/hadoop-httpfs
```

### Remove process ID's

```
/var/run/hadoop
/var/run/hbase
/var/run/hadoop-yarn
/var/run/hadoop-mapreduce
/var/run/hive
/var/run/oozie
/var/run/zookeeper
/var/run/flume
/var/run/hive-hcatalog
/var/run/falcon
/var/run/webhcat
/var/run/knox
```

### Remove zookeeper DB

```
/usr/lib/flume
/usr/lib/storm
/var/lib/hadoop-hdfs
/var/lib/hadoop-yarn
/var/lib/hadoop-mapreduce
/var/lib/flume
/var/lib/knox
```

### Remove oozie-temp folder

```
/var/tmp/oozie
```


P.S. Even if we unistall and remove all the directories, fresh install will fail. This is due to symlinks created by the system and so when we fresh install it, system can find the symlinks and thus will not install the components or libraries.


Drop database for ambari
