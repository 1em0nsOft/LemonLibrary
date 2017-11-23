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


