## Installation of Server

### PreRequisites

1. Open Git Bash

2. Enter into the gateway by __ssh bhavin@openstack.cloudwick.com__, then password: _Cloudw!ck_

3. Enter into server by __ssh root@172.16.0.132__, then password: hadoop123

4. Check if we are using correct OS
  * _cat /etc/issue_   or _cat /etc/redhat-release_

5. Check common Prerequisites
  * Install _wget_, _unzip_, _curl_, _tar_;
  * Firewall
    * Save present firewall configuration by `sudo iptables-save > /etc/sysconfig/iptables`
    * Stop firewall: `sudo service iptables stop` & `sudo chkconfig iptables off`
    * Check if firewall disable `sudo service iptables status`
    * Disable ipv6 tables: `service ip6tables stop` & `chkconfig ip6tables off` for permanently stop. 
    * Disable SELinux `sudo /usr/sbin/setenforce 0`
      `sudo sed -i.old s/SELINUX=enforcing/SELINUX=disabled/ /etc/selinux/config`
    * Set hostname `sudo hostname [ ... ]`
    * Validate username: `uname -a`
    * Check config: `/sbin/ifconfig`
    * We install NTP because we want all our node to synchronize with each other,
      * Install NTP: `yum install ntp`, update NTP: `ntpupdate [ntp_server]`, Start ntpd: `service ntpd start`
      * To start ntp at boot: `chkconfig ntpd on  

6. Check if we can mount device
  * `lsblk`

7. Check if transparent huge pages (to disable go to point 10)
  * `cat /proc/sys/vm/nr_hugepages` output should be __0__

8. Install Java
  * Following is link to install java 1.8 [here](http://tecadmin.net/install-java-8-on-centos-rhel-and-fedora/#)
  * `cd opt`
  * `wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.rpm -O jdk-8u101-linux-x64.rpm`
  * `rpm -ivh jdk-8u101-linux-x64.rpm`
  * `alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_101/bin/java 200000`

9. Install MySql
  * `yum install mysql-server`
  *  To check status of mysql: `service mysqld status`
  * `service mysqld start` to start the mysqld

10. To tune hadoop:
  * It is important to tune few parameter of memory to optimize hadoop. [Here](http://mapredit.blogspot.co.uk/2014/11/hadoop-server-performance-tuning.html) is the wonerful link.
    * To disable swappiness:
    ` sysctl -w vm.swappiness=0`
    `echo “vm.swappiness = 0” >> /etc/sysctl.conf `
  * Disable Huge Tansparent Pages:
    `echo never > /sys/kernel/mm/transparent_hugepage/enabled`
    `if test -f /sys/kernel/mm/redhat_transparent_hugepage/enabled; then
echo never > /sys/kernel/mm/redhat_transparent_hugepage/enabled
fi`

11. `yum -y upgrade openssl`


###Installation

1. Mysql-connector
  * `yum install mysql-connector-java`

2. Download Ambari
  * `cd /etc/yum.repos.d/`
  * `wget http://public-repo-1.hortonworks.com/ambari/centos6/2.x/updates/2.4.0.1/ambari.repo`

3. Start mysql
  * `mysql -u root -p`, press enter and no password
  * `mysql_secure_installtion` and delete anonymous user

4. Create database
  1. Hive
```mysql
CREATE USER 'hive'@'master' IDENTIFIED BY 'hive';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'localhost' with grant option;
CREATE USER 'hive'@'%' IDENTIFIED BY 'hive';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'%' with grant option;
CREATE USER 'hive'@'<FQDN of the HiveMetaStoreHost>' IDENTIFIED BY
'hive';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'<* FQDN of the
HiveMetaStoreHost >' with grant option; 
```

  2. Ambari
```mysql
CREATE USER 'ambari'@'master2.cloudwick.com' IDENTIFIED BY 'ambari';
GRANT ALL PRIVILEGES ON *.* TO 'ambari'@'master2.cloudwick.com' with grant option;
CREATE USER 'ambari'@'%' IDENTIFIED BY 'ambari';
GRANT ALL PRIVILEGES ON *.* TO 'ambari'@'%' with grant option;
CREATE USER 'ambari'@'<FQDN of the AmbariServerHost>' IDENTIFIED BY
'ambari';
GRANT ALL PRIVILEGES ON *.* TO 'ambari'@'<* FQDN of the
AmbariServerHost >' with grant option; 
```

  3. Oozie
```mysql
CREATE USER 'oozie'@'localhost' IDENTIFIED BY 'oozie';
GRANT ALL PRIVILEGES ON *.* TO 'oozie'@'localhost' with grant option;
CREATE USER 'oozie'@'%' IDENTIFIED BY 'oozie';
GRANT ALL PRIVILEGES ON *.* TO 'oozie'@'%' with grant option;
CREATE USER 'oozie'@'<FQDN of the OozieServerHost>' IDENTIFIED BY
'oozie';
GRANT ALL PRIVILEGES ON *.* TO 'oozie'@'<* FQDN of the OozieServerHost
>' with grant option;
```

5. Install Ambari 
  * `yum install ambari-server`
  * `yum install ambari-agent`
  * `ambari-server setup`, select custom jdk and select java path ; this also install new JDK, you can use previously installed JDK by
	'ambari-server setup -j /usr/java/latest/`, [Here] (https://developer.ibm.com/hadoop/blog/2015/10/30/configure-ambari-hadoop-cluster-use-single-mysql-database-instance/) is good link.


6. Select Mysql as database
  * `mysql -u ambari -p`, Press enter with no password
  * Create database

```mysql
create database ambari;
use ambari;
source /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql;
```

7. ambari-agent.ini modify
  * `sudo sed -i.old s/hostname=localhost/hostname=bmaster1.cloudwick.com/ /etc/ambari-agent/conf/ambari-agent.ini`

8. Start ambari server
  * `sudo service ambari-server start`





### Miscellaneous

* To delete users in mysql
  * DROP USER xyz
  * DELETE FROM mysql.user WHERE user = xyz
  * DELETE FROM mysql.user WHERE host = xyz

we set hostname in: /etc/sysconfig/network

we add hosts in: /etc/hosts

Normal copy: `CP oldfile newfile`

To [Secure copy](http://www.hypexr.org/linux_scp_help.php)-  `SCP user@host:oldfile user@host:newfile`
OR
`scp /etc/hosts root@172.16.0.107:/etc/`

* To get version information
  `yum info ambari-server`
  `yum info ambari-agent`

* MySQL issue:
  1. We should make sure to remove anonymous users.

* On all datanode
  1. `/etc/ambari-agent/conf/ambari-agent.ini` and change HostName to ambari-server hostname.

* To Delete Postgresql database
  1. `su -u postgres`
  2. `psql`
  3. `drop database ambari`
  
