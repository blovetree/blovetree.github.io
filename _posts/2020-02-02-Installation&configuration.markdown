---
layout:     post
title:      "Installation&configuration"
date:       2020-02-02
author:     "Dylan"
catalog: true
tags:
    - Installation&configuration
    - note
---


## docker

> [UBuntu 16.04下安装Docker](https://yq.aliyun.com/articles/675833/)

用官方脚本安装docker

`sudo curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun`


#### 装mysql

```
docker pull mysql:latest
docker images
docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql mysql
docker ps
docker exec -it mysql bash
```


#### 装redis

```
docker pull redis:latest
docker images
docker run -itd --name redis -p 6379:6379 redis
docker ps
docker exec -it redis /bin/bash
```