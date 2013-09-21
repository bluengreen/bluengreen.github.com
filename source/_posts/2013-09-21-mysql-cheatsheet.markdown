---
layout: post
title: "MySQL Cheatsheet"
date: 2013-09-21 04:41
comments: true
categories: 
---

My MySQL cheatsheet. Stuff I find myself needing but never remembering. :) 

<!-- more -->

## Backup 


```
# one database
$ mysqldump -h localhost -u root -pmypassword databasename > dumpfile.sql
```

```
# all databases 
$ mysqldump -h localhost -u root -pmypassword --all-databases > dumpfile.sql

```

## Restore

```
# restore one database
$ mysql -u root -pmypassword databasename < dumpfile.sql
```

```
# restore all databases
$ mysql -u root -pmypassword < dumpfile.sql
```

## Set admin password

```
$ mysqladmin -u root password newpassword
```

## Change user password

```
$ mysql -u root -p
mysql> SET PASSWORD FOR 'user'@'hostname'=PASSWORD('passwordhere');
mysql> FLUSH PRIVILEGES;
```

## Recover root password

```
$ mysql.server stop
$ mysqld_safe --skip-grant-tables &
$ mysql -u root
mysql> USE mysql;
mysql> UPDATE USER SET password=PASSWORD("newrootpassword") WHERE User='root';
mysql> FLUSH PRIVILEGES;
mysql> exit 
# mysql.server stop
# mysql.server start
```

[MySQL Documentation](http://dev.mysql.com/doc/refman/5.6/en/)

[MySQL mysqldump reference](http://dev.mysql.com/doc/refman/5.6/en/mysqldump.html)
