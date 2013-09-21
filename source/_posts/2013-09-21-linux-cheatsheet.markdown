---
layout: post
title: "Linux CheatSheet"
date: 2013-09-21 01:26
comments: true
categories: 
---

Helpful reminders to linux commands

<!-- more -->

## Tar/gzip 

{% codeblock %}
tar -czvf archive.tgz directory_to_archive 
{% endcodeblock %}

## Find stuff 

Find files older than 3 days old and remove them.

```
find . -mtime +3 | xargs rm -Rf
```

## Move stuff

Move files that start with any character between a and m into a directory called dir

```
mv [a-m]*  dir
```


## List stuff 

List number of files in the current working directory.

```
ll | wc -l
```

## Transfer stuff 

Copy local file to remote server. 

```
scp filename.txt username@servername.com:/path/to/my/remote/dir/filename.txt 
```

Copy remote file to local file system.
```
scp username@servername.com:/path/to/my/remote/dir/filename.txt ../local/filename.txt 
```


## sysadmin 

Look for a process with the keyword mysql

```
ps -ef | grep mysql  
```

List iptable rules

```
iptables -L 
```

