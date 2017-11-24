### 下载
> 这里我们下载的是atlassian官方提供的镜像

```bash
[root@zhujiwu home]# docker pull registry.docker-cn.com/atlassian/confluence-server
```

### 运行

> -v /data/data/confluence:...是你要将容器中的数据文件保存在的本地宿主机的目录地址

```
docker run -v /data/data/confluence:/var/atlassian/application-data/confluence --name="confluence" -d -p 8090:8090 -p 8091:8091 --restart always 3e2c26db9138
```

### 测试
docker run之后访问 http://yourip:8090 即可自动进入confluence的初始化界面