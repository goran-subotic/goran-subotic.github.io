---
author: goran
layout: post
title:  "How to setup Tigase 7.0.2 cluster"
date:   2015-08-02 22:32:00
categories: xmpp jabber tigase
tags: tigase ubuntu chat xmpp jabber mysql mongodb cluster
#image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
visible: 1
---

##Introduction

[Tigase](http://www.tigase.net/content/tigase-xmpp-server) is an open source chat server written for the XMPP (aka Jabber) protocol. It offers high performance, and easy clustering which makes it highly scalable.

Before you reach hundreds of thousands of online users on your chat server, you might want to make sure that you will be able to support this kind of traffic. 

This tutorial will guide you through the steps of setting up a Tigase cluster of two nodes.

##Prerequisites

Before you begin this guide, you should have Tigase installed on a single machine. If you didn't do that already please follow the [previous tutorial](/chat/2015/07/31/how_to_install_tigase_on_ubuntu.html).

Another prerequisite is that you have second Ubuntu machine up and running.

##Step 1 - Set up the hostnames and hosts file

In order for nodes to "see" each other, the same values must be set up as machines' hostnames and in the hosts file.

Go to the first machine and type:

```
sudo nano /etc/hostname
```

Replace whatever is there with: machine1

Now edit the hosts file:

```
sudo nano /etc/hosts
```

Modify the entry with the address 127.0.1.1 and replace the existing hostname with machine1.

Add a new entry:

```
<ip of the second machine>    machine2
```

Now, go to second machine and repeat these steps, but use machine2 as the hostname. Also add new entry as:

```
<ip of the first machine>    machine1
```

##Step 2 - Install the Tigase on second machine

For the second machine repeat almost all the steps from the [first tutorial](/chat/2015/07/31/how_to_install_tigase_on_ubuntu.html).

You can skip the installation of mysql-server and mysql-client packages.

During the installation, please make sure that all the configuration is the same. The most important thing is that you set the same "ubuntu" domain on the second machine as well.

Also, when Tigase installer asks you to enter:

```
Database host or IP: [localhost]
```

Please enter the hostname of the first machine (machine1).

*If you encounter any problem with database configuration later on, you can find the configuration in the file:
~/Tigase-7.0.2-b3821/etc/init.properties*

##Step 3 - Allow access to mysql

In order for mysql to listen for outside connections we must edit mysql configuration file.
Log in to the first machine. 
Open and edit my.cnf file:

```
sudo nano /etc/mysql/my.cnf
```

Find and comment the line:

```
bind-address           = 127.0.0.1
```

Save changes and restart mysql:

```
sudo service mysql restart
```

##Step 4 - Run both nodes and test if everything is ok

This should be everything needed for Tigase cluster to run.

Nodes will register on startup and Tigase will manage the list of active nodes by using "cluster_nodes" table in the database.

You can test this by starting both nodes. Run the following on both machines:

```
./scripts/tigase.sh start etc/tigase.conf
```

Now, login to the mysql database on the first machine:

```
mysql -u root
use tigasedb;
select * from cluster_nodes;
```

You should see something like this:

```
*************************** 1. row ***************************
   hostname: ubuntu
   password: 66d4cf68ff3598ee03d9e25a0b26acfb796b3efe4077b32579bb12a804ac72e1
last_update: 2015-07-31 14:07:23
       port: 5277
  cpu_usage: 0.2907822
  mem_usage: 62.136894
*************************** 2. row ***************************
   hostname: ubuntu2
   password: 5c4c7419d8ad108737a945d92d989b42c9480ad81aca92803fe8b5f35efba4a4
last_update: 2015-07-31 14:07:29
       port: 5277
  cpu_usage: 0.6053269
  mem_usage: 51.349678
2 rows in set (0.01 sec)
```







