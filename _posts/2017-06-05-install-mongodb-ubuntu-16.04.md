---
layout: post
title: how to install MongoDB in Ubuntu 16.04
date: 2017-06-05 20:36:00 +0900
categories:
  - linux
  - redis
tags:
  - ubuntu 16.04
  - redis
  - tutorial
  - installation
comments: true
---

## tl;dr

just see [official doc](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) or the following.

```shell
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
$ echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
$ sudo apt update
$ sudo apt install mongodb-org

$ sudo service mongod [start|stop|restart]

$ mongo
```

`mongo` opens the mongo shell, then you can test the basic usage, for example [the official tutorial](https://docs.mongodb.com/getting-started/shell/client/).
