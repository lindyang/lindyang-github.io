---
title: "Jira"
date: 2023-07-14T17:44:29+08:00
tags: [misc]
---

## [下载](https://product-downloads.atlassian.com/software/jira/downloads/atlassian-jira-software-8.0.2-x64.bin "jira-software-8.0.2-x64.bin 351M")
```
curl -OC - https://product-downloads.atlassian.com/software/jira/downloads/atlassian-jira-software-8.0.2-x64.bin;
```

## 运行安装文件


## 遵照[指导手册](https://confluence.atlassian.com/adminjiraserver/installing-jira-applications-on-linux-938846841.html "installation guide")

```
Installation Directory: /opt/atlassian/jira
Home Directory: /var/atlassian/application-data/jira
HTTP Port: 8080
RMI Port: 8005
Install as service: Yes

https://confluence.atlassian.com/adminjiraserver/connecting-jira-applications-to-mysql-5-7-966063305.html
create user 'jiradbuser'@'%' IDENTIFIED BY 'jiradbuser';
CREATE DATABASE IF NOT EXISTS jiradb DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
grant all on jiradb.* to 'jiradb'@'%';
mysqld --help

https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java_8.0.15-1ubuntu16.04_all.deb

apt-get install libmysql-java

Q：Could not find driver with class name: com.mysql.jdbc.Driver
A：把mysql-connector-java-5.1.38.tar.gz 放到/usr/local/jira/bin和/usr/local/jira/lib/下。

shutdown start;
```

```
It was the JDBC connector that was the problem. I downloaded and installed the latest (version 8.0.13) but it was causing the error. Installing version 5.1.47 works just fine, even though it isn't recommended for use with MySQL 5.6, which I'm using.

But, using that connector, I have a database installed and Jira is functioning as it should.
```

http://my.atlassian.com/  license key


https://blog.csdn.net/XunCiy/article/details/81981944

https://stackoverflow.com/questions/53907766/jira-clean-install-you-have-specified-a-database-that-is-not-empty


