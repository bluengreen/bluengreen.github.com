---
layout: post
title: "MySQL Setup replication"
date: 2013-09-21 04:41
comments: true
categories: [mysql, linux]
---


Basic steps for setting up mysql replication on rackspace CloudServers using a RHEL/Centos distribution of linux.

## MASTER SERVER CONFIGURATION

Setup firewall rules on the master database server to accept incoming requests from the internal network card attached to your CloudServer instance. This is assuming that the slave database server is also a rackspace CloudServer and within the same region. 

``` bash
# firewall rules for master
# allows the slave servers to connect to the master 
# via rackspace servicenet
iptables -A INPUT -i eth1 -p tcp  --dport 3306 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth1 -p tcp --sport 3306 -m state --state ESTABLISHED -j ACCEPT
service iptables save
```


Configure the master mysql database server. On a RHEL/Centos linux distro the mysql configuration file is in /etc. Below is an example configuration. The add the settings under the master setup section. The most important of these are the server-id and the log-bin. Every server needs a server-id. Mysql uses the log-bin to sync the slave servers. 

``` 
# configure mysql conf file for replication
sudo vi /etc/my.cnf 


[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql

# master setup
server-id=1
log-bin=master-bin.log
innodb_flush_log_at_trx_commit=1
sync_binlog=1
binlog_do_db=mydb

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

Next, we have to create a user for the slave servers to use to login and connect to the database server.

``` 
# start the mysql client
mysql -uroot -p'rootpassword'

# create the replication user in mysql 
> GRANT REPLICATION SLAVE ON *.* TO 'replication_user'@'10.1.1.2' IDENTIFIED BY '<your password>'; 
> FLUSH PRIVILEGES;
```

Now show the status and get the current log and position of the master database server.  
If your are on a very active database server. You will have additional steps stopping locks and writes. However, for the purpose of this setup we will assume you dont have any activity yet. 


```
# show status and grab log and position
> SHOW MASTER STATUS;
```


## SLAVE SERVER CONFIGURATION

Now with the master setup and the log and position of the master database we can now configure our slave database server. 


```
######################################### 
# configure slave my.cnf 

[mysqld]
server-id=2
master-host=10.1.1.1
master-user=replication_user
master-password=<your password>
master-connect-retry=60
replicate-do-db=mydb
```

Verify you can connect to the master server by using the mysql client on the slave server. 

```
#########################################
# verify you can connect to the master

mysql -h 10.1.1.1 -u repl -p'<your replication user password>'
> exit
```
If that was successful we can login to the local mysql database server on the slave server and start the replication. If you weren't able to login to the master database server from the slave server double check your firewall rules and user settings in the master database server. 

```
mysql -uroot -p'rootpassword'

# change master to start replication 
> SLAVE STOP; 
> CHANGE MASTER TO MASTER_HOST='10.1.1.1', 
>                 MASTER_USER='replication_user', 
>                 MASTER_PASSWORD='<your password>', 
>                 MASTER_LOG_FILE='master-bin.000003', 
>                 MASTER_LOG_POS=106;
>START SLAVE;
>SHOW SLAVE STATUS\G;
```

If all is successful. You should see a screen like this. 


```
SHOW SLAVE STATUS\G;
*************************** 1. row ***************************
             Slave_IO_State: Waiting for master to send event
                Master_Host: 10.176.41.72
                Master_User: repl
                Master_Port: 3306
              Connect_Retry: 60
            Master_Log_File: mysql-bin.000003
        Read_Master_Log_Pos: 314
             Relay_Log_File: mysqld-relay-bin.000003
              Relay_Log_Pos: 235
      Relay_Master_Log_File: mysql-bin.000003
           Slave_IO_Running: Yes
          Slave_SQL_Running: Yes
            Replicate_Do_DB: 
        Replicate_Ignore_DB: 
         Replicate_Do_Table: 
     Replicate_Ignore_Table: 
    Replicate_Wild_Do_Table: 
Replicate_Wild_Ignore_Table: 
                 Last_Errno: 0
                 Last_Error: 
               Skip_Counter: 0
        Exec_Master_Log_Pos: 314
            Relay_Log_Space: 235
            Until_Condition: None
             Until_Log_File: 
              Until_Log_Pos: 0
         Master_SSL_Allowed: No
         Master_SSL_CA_File: 
         Master_SSL_CA_Path: 
            Master_SSL_Cert: 
          Master_SSL_Cipher: 
             Master_SSL_Key: 
      Seconds_Behind_Master: 0
```


