---
author: goran
layout: post
title:  "How to install Tigase XMPP server 7.0.2 on Ubuntu 14.04.2"
date:   2015-07-31 14:34:25
categories: chat
tags: tigase ubuntu chat xmpp jabber mysql mongodb cluster
#image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
visible: 1
---
##Introduction

This tutorial will cover the steps needed to install the Tigase 7.0.2 on Ubuntu 14.04.2. I will show how to install Tigase with mysql database and what it takes to replace it with mongodb. 

[Tigase](http://www.tigase.net/content/tigase-xmpp-server) is an open source chat server written for the XMPP (aka Jabber) protocol. It offers high performance, and easy clustering which makes it highly scalable.

##Prerequisites

Before you begin this guide, you should have a regular, non-root user with sudo privileges configured on your server. 

When you have an account available, log in as your non-root user to begin.

##Step One - Install Java 8 JDK

The Tigase version 7.0.2 is built with Java 8, so you will need Oracle Java 8 JDK.

In order to retrieve Oracle Java JDK, first run these couple of lines:

{% highlight js %}
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
{% endhighlight %}

They will add external webupd8team package repository and retrieve updated lists of packages.

Good, now you have access to Oracle Java 8 JDK installer. Run the following:

{% highlight js %}
sudo apt-get install oracle-java8-installer
{% endhighlight %}

This will install the Java 8 JDK to the:

```
/usr/lib/jvm/java-8-oracle
```

##Step Two - Install mySql

Tigase supports several databases: PostgreSQL, mySql, Derby, SqlServer, mongodb.

For this tutorial I chose mySql.

Just run:

{% highlight js %}
sudo apt-get install mysql-server mysql-client
{% endhighlight %}

It will install mysql database and client tool. 

During the installation the installer will ask you to enter password for the "root" user. For testing purposes, you can leave the "root" user without password.

##Step Three - Install Tigase 7.0.2

To install Tigase you will have to first download it from their site.

Go to /tmp directory:

{% highlight js %}
cd /tmp
{% endhighlight %}

Then download the Tigase server 7.0.2 jar:

{% highlight js %}
wget https://projects.tigase.org/attachments/download/2663/tigase-server-7.0.2-b3821.jar
{% endhighlight %}

*The list of Tigase builds can be found [here](https://projects.tigase.org/projects/tigase-server/files)*

Now, just run it:

{% highlight js %}
java -jar tigase-server-7.0.2-b3821.jar -console
{% endhighlight %}

-console flag means the installation process will run in the terminal window. If you have GUI, you can omit the -console flag and the GUI representation of the Tigase installer will run.

```
The installed application needs a JDK. A java runtime environment (JRE) will be not sufficient.
Enter path:
```

Enter path: /usr/lib/jvm/java-8-oracle


```
The wizards you want to execute Installation of the Tigase Server
[on, off]
```

Type on and hit Enter.

```
Configuration of the Tigase Server
[on, off]
```

Type on and hit Enter.

Next, the installer will show the release document. Press ENTER several times, until you reach the end of the document.

In the next step, the installer will display the license agreement. Press ENTER several times, until you reach the end of the document.
At the end of the document you will be asked to accept the terms of the license agreement.

```
Select target path [/tmp]
```

Enter: /home/[username]/Tigase-7.0.2-b3821

*Replace [username] with your Ubuntu username.*


```
Select the packs you want to install:
1 => Base, The base files
2 => Unix Files, Files needed to run the server on Unix like systems
3 => [x] Docs, The documentation
4 => [x] Extras, Extras libraries, MUC, PubSub, STUN, Socks5, Message Archiving, ACS
5 => [x] Groovy scripting, Various Groovy scripts extending server functionality
6 => [x] Derby Database (DB included), Derby database (included) and JDBC driver
7 => [x] MySQL Database driver (DB not included), MySQL JDBC driver (MySQL has to be installed separately)
8 => [x] PostgreSQL Database driver (DB not included), PostgreSQL JDBC driver (PostgreSQL has to be installed separately)
9 => [x] SQL Server Database driver (DB not included), SQL Server JDBC driver (SQL Server has to be installed separately)
10 => [x] MongoDB Database support (DB not included), Libraries need to use MongoDB from Tigase (includes driver)
11 => [ ] OSGi dependencies, If you intend to run Tigase in OSGi mode you need additional libraries. Enabling this Pack will also setup configuration files to start Tigase XMPP server in OSGi mode.
12 => [ ] HTTP API Component, Interface for the service administration, monitoring and exchange data between Tigase and external system
13 => [ ] Sources, The server source files, tools and libraries sources are not included
r => Redisplay menu
d => Done
```

Choose action: d

``` 
0  [x] Default installation
1  [ ] Default plus extra components
2  [ ] Session Manager only
3  [ ] Network connectivity only
input selection:

```

Enter 0 and hit Enter.

For the testing purposts, leave the next three settings as default:

```
Your XMPP (Jabber) domains [ubuntu]
Server administrators [admin@ubuntu] 
Admin password [tigase]
```

In the next step installer will ask you to choose your database.

```
0  [x] Derby (built-in database)
1  [ ] MySQL
2  [ ] PostgreSQL
3  [ ] SQLServer
4  [ ] Other...
input selection:
```

To select MySQL, enter 1 and hit Enter.

``` 
Advanced configuration options
[on, off] 
```

Enter off and hit Enter.

In the following steps, you will be asked to enter mysql configuration. For the testing purposes, just leave everything as default:

```
Super user account name: [root] 
WARNING: password will be visible while entering
Super user password: 
WARNING: password will be visible while entering
Retype password: 
-------------------
MySQL database details. It will be created automatically if it does not exist.
Database account: [tigase] 
Account password: [tigase12] 
Database name: [tigasedb] 
Database host or IP: [localhost]
```

The Tigase installation should finish with the following output:

```
All users added
Post installation actions  
Finalizing... completed OK
Loading socks5 component schema  
Loading socks5 schema... completed OK
Loading PubSub component schema  
Loading PubSub schema... completed OK

Install was successeful
application installed on /home/username/Tigase-7.0.2-b3821
[ Console installation done ]
$
```

The Tigase database should been created with all the tables and necessary users.
You can check this by logging into the mysql. Type:

```
mysql -u root
mysql> show databases;
```

You should see tigasedb in the list of databases.

```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| tigasedb           |
+--------------------+
4 rows in set (0.00 sec)
```

Type:

```
mysql> use tigasedb;
mysql> show tables;
```

```
+--------------------------+
| Tables_in_tigasedb       |
+--------------------------+
| short_news               |
| tig_nodes                |
| tig_pairs                |
| tig_pubsub_affiliations  |
| tig_pubsub_items         |
| tig_pubsub_jids          |
| tig_pubsub_nodes         |
| tig_pubsub_service_jids  |
| tig_pubsub_subscriptions |
| tig_socks5_connections   |
| tig_socks5_users         |
| tig_users                |
| xmpp_stanza              |
+--------------------------+
13 rows in set (0.00 sec)
```

These are the tables Tigase will need to run.

#Step Four - Running the chat server

To run the Tigase, go to the Tigase installation directory:

```
cd ~/Tigase-7.0.2-b3821
```

... and execute:

```
./scripts/tigase.sh start etc/tigase.conf
```

The output should be:

```
Starting Tigase: 
Tigase running pid=16379
username@ubuntu:~/Tigase-7.0.2-b3821$ nohup: redirecting stderr to stdout
```

#Step Five - Test if it works

Before you try to connect with a Jabber client, like Psi, Pidgin or Trillian, you must first register/create a test user.

You can register a new user on your Tigase server by using Psi, but I am using Trillian which does not support user registration. Instead I will create the user on the server directly by using Tigase's mysql stored procedures.

```
mysql -u root
mysql>use tigasedb;
mysql> call TigAddUserPlainPw('test-user@ubuntu', 'pass123');
+------+
| uid  |
+------+
|    9 |
+------+
1 row in set (0.01 sec)
Query OK, 0 rows affected (0.01 sec)
```

Please notice that we used the same "ubuntu" domain as we have set during the configuration steps of Tigase.
On my windows machine I can now use Trillian to connect to my new chat server. I will use user credentials I have just created and connect to the IP address of my Ubuntu machine. The port is the default 5222.

In the [next post](/xmpp/jabber/tigase/2015/08/03/how_to_setup_tigase_cluster.html) I covered few simple steps that will enable you to establish Tigase cluster for handling larger traffic volumes.
