---
layout: post
title: How to install Redis in Ubuntu 16.04
date: 2017-06-05 20:36:00 +0900
categories:
  - Linux
  - Redis
tags:
  - Ubuntu 16.04
  - tutorial
  - installation
comments: true
---

## tl;dr

```shell
$ sudo apt install redis-server

$ redis-cli
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set mykey somevalue
127.0.0.1:6379> get mykey
somevalue
```

## get it from the official repository

It is always safer and simpler to install something from the official repository.

```shell
$ sudo apt install redis-server
```

This command will install two packages: redis-server and redis-tools (its client tool).

## check installed

After the installation is done, you can confirm it via checking the services list.

```shell
$ service --status-all | grep redis
 [ + ]  redis-server
```

From the above result you notice that the service of redis-server is already installed with the installation of the package and the service is also started. If you want to stop this service at this point, the following command will do.

```shell
$ sudo service redis-server stop
```

## simple usage of redis-cli

You can test the basic usage of Redis via redis-cli which is installed with redis-server package. With the redis-server started as a service, redis-cli will open the session.

```shell
$ redis-cli
127.0.0.1:6379> ping
PONG
```

It is a normal behaviour of the session to print PONG as a answer of your `ping`. Then test the following set and get commands as an example.

```shell
127.0.0.1:6379> set mykey somevalue
127.0.0.1:6379> get mykey
somevalue
```
