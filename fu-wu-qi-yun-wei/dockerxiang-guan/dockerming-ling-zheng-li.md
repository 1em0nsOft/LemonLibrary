# Docker命令整理

### 启动docker

```
[root@db02 client]# systemctl start docker
[root@db02 client]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since 二 2017-11-07 10:40:16 CST; 1s ago
     Docs: http://docs.docker.com
 Main PID: 11272 (dockerd-current)
   CGroup: /system.slice/docker.service
           ├─11272 /usr/bin/dockerd-current --add-runtime docker-runc=/usr/libexec/docker/docker-runc-current --def...
           └─11283 /usr/bin/docker-containerd-current -l unix:///var/run/docker/libcontainerd/docker-containerd.soc...
```

#### 获取镜像

```
# docker pull NAME[:TAG]
[root@localhost ~]# docker pull nano/node.js:0.10
```

#### 查看本地所有镜像

```
# docker images
[root@localhost ~]# docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
das                      0.2                 e0823ecf5b59        2 hours ago         668 MB
daocloud.io/node         5                   12b4a63115bc        5 months ago        648 MB
```

#### 构建一份新的镜像， build时会读取 Dockerfile配置

```
# docker build -t <newimagename> <目录>

[root@localhost ~]# docker build -t das .

```

#### 删除一个镜像

```
# docker rmi IMAGE [IMAGE...]
[root@localhost ~]# docker rmi e0823ecf5b59

```

#### 删除所有未打 dangling 标签的镜像

```
[root@localhost ~]# docker rmi $(docker images -q -f dangling=true)
```

#### 删除所有镜像

```
[root@localhost ~]# docker rmi $(docker images -q)

```

#### 创建一个新的容器但不启动它, 用法同 docker run

```
# docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
[root@localhost ~]# docker create das

# 或者：
[root@localhost ~]# docker create e0823ecf5b59

```

#### 启动Container容器

```
# docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
[root@localhost ~]# docker run -ti das
# 或者：
[root@localhost ~]# docker run -ti e0823ecf5b59

```

#### 启动Container容器并映射到外网

```
# docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
[root@localhost ~]# docker run -ti -p 8000:8000 das
# 或者：
[root@localhost ~]# docker run -ti -p 8000:8000 e0823ecf5b59

```

#### 查看容器列表

```
docker ps -a

[root@localhost ~]# docker ps -a
CONTAINER ID     IMAGE            COMMAND        CREATED           STATUS           PORTS                    NAMES
c50a9c160f93     e0823ecf5b59     "npm start"    45 minutes ago    Up 45 minutes    0.0.0.0:8000->8000/tcp   kickass_bohr>>
```

#### 查看容器的PID

```
[root@localhost ~]# docker inspect --format {{.State.Pid}} c50a9c160f93
27432
[root@localhost ~]#

```

#### 根据容器的PID进入容器

```
[root@localhost ~]# nsenter -t 27432 --mount --uts --ipc --net --pid

```

#### 停止一个运行中的容器

```
# docker stop  [OPTIONS] CONTAINER [CONTAINER...]
[root@localhost ~]# docker stop  c50a9c160f93

```

#### 启动一个或多个已经被停止的容器

```
# docker start [OPTIONS] CONTAINER [CONTAINER...]
[root@localhost ~]# docker start c50a9c160f93

```

#### 重启容器

```
# docker restart [OPTIONS] CONTAINER [CONTAINER...]
[root@localhost ~]# docker restart c50a9c160f93

```

#### 杀死一个正在运行的容器

```
# docker kill [OPTIONS] CONTAINER [CONTAINER...]
[root@localhost ~]# docker kill c50a9c160f93

```

#### 杀死所有正在运行的容器

```
[root@localhost ~]# docker kill $(docker ps -a -q)

```

#### 删除所有已经停止的容器

```
[root@localhost ~]# docker rm $(docker ps -a -q)

```

#### 发布镜像

```
# docker tag 镜像名:镜像版本 DockerHub用户名/镜像名:镜像版本号
[root@localhost ~]# docker tag das:0.1 maosiyu/das:0.1
# docker push DockerHub用户名/镜像名:镜像版本号
[root@localhost maosiyu]# docker push maosiyu/das:0.1

```

#### 导出容器

```
[root@localhost maosiyu]# docker export c50a9c160f93 
>
 das0.3.tar

```

#### 导入容器

```
[root@localhost mao_siyu]# cat das0.3.tar | docker import - das:0.3

```

#### 保存镜像

```
[root@localhost maosiyu]# docker save das:0.3 
>
 das0.3.tar

```

#### 载入镜像

```
[root@localhost mao_siyu]# docker load -i das0.3.tar

```

#### 实时查看容器日志

```
[root@localhost mao_siyu]# docker logs -f -t c50a9c160f93

```



