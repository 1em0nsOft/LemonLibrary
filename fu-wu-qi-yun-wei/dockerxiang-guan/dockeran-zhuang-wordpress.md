### 查看Docker Hub官网wordpress 镜像 https://hub.docker.com/r/library/wordpress/

### 当前环境：
```
服务器地址: 10.32.156.51
```

### 下载 wordpress 镜像文件

``` ruby
[root@localhost ~]# docker pull registry.docker-cn.com/library/wordpress
[root@localhost ~]#
[root@localhost ~]# docker images
REPOSITORY                                 TAG                 IMAGE ID            CREATED             SIZE
registry.docker-cn.com/library/wordpress   latest              fcf3e41b8864        10 days ago         408MB
[root@localhost ~]#
```


### 运行

``` ruby
[root@localhost ~]# docker run -p 8088:80 -d fcf3e41b8864
d04dc214cc8b64a4b6e832f7ee289453ad12d201f5f881ed9554ab886c048fb4
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
d04dc214cc8b        fcf3e41b8864        "docker-entrypoint..."   8 seconds ago       Up 6 seconds        0.0.0.0:8088->80/tcp     musing_volhard
[root@localhost ~]#
```


#### 浏览器访问 http://10.32.156.51:8088/ 根据提示自己安装配置 mysql