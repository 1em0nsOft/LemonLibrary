### 下载
> 这里我们下载的是atlassian官方镜像

```bash
[root@zhujiwu home]# docker pull registry.docker-cn.com/atlassian/bitbucket-server
Using default tag: latest
latest: Pulling from cptactionhank/atlassian-bitbucket
3e17c6eae66c: Pull complete
fdfb54153de7: Pull complete
a4ca6e73242a: Pull complete
93bd198d0a5f: Pull complete
ca4d78fb08d6: Pull complete
ad3d1bdcab4b: Pull complete
4853d1e6d0c1: Pull complete
49e4624ad45f: Pull complete
bcbcd4c3ef93: Pull complete
775a7c81596c: Pull complete
6f051a591288: Pull complete
Digest: sha256:3469b5bebb491ecc45b0b0d2fc7e685ef92cef5bb4f154ca9b67e86c3b85ce8b
Status: Downloaded newer image for registry.docker-cn.com/catlassian/bitbucket-server
:latest
[root@zhujiwu home]# docker images
REPOSITORY                                                 TAG                 IMAGE ID            CREATED             SIZE
registry.docker-cn.com/atlassian/bitbucket-server
   latest              e708ca3f0564        3 days ago          1.04GB
```

### 运行

```bash
[root@zhujiwu home]# docker run -d -p 7990:7990 --restart always -v /home/bitbucket:/var/atlassian/application-data/bitbucket --name msy-bitbucket e708ca3f0564
2936b718c277ccb4381152fcaaa2b69dfe263057fb2840efbbf0f9a55313171a
[root@zhujiwu home]#
[root@zhujiwu home]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                              NAMES
2936b718c277        e708ca3f0564        "/docker-entrypoin..."   5 minutes ago       Up 4 minutes        0.0.0.0:7990->7990/tcp, 7999/tcp   msy-bitbucket
```

### 进入容器bash

> 命令 格式：docker exec -it CONTAINER ID /bin/bash

```
[root@zhujiwu home]# docker exec -it 2936b718c277 /bin/bash
root@2936b718c277:/var/atlassian/bitbucket#
```

### 若提示数据库UTF8异常，使用以下SQL命令修改bitbucket对应的数据库

```SQL
ALTER DATABASE bitbucket_db CHARACTER SET utf8 COLLATE utf8_bin
```

### 测试

打开 `http://yourip:port` 即可进入bitbucket的自动初始化界面



