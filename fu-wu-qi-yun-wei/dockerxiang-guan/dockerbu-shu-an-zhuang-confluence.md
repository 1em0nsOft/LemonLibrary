### 下载
> 这里我们下载的是atlassian官方提供的镜像

```bash
[root@zhujiwu home]# docker pull registry.docker-cn.com/atlassian/confluence-server
```

### 运行

```
docker run -v /data/data/confluence:/var/atlassian/application-data/confluence --name="confluence" -d -p 8090:8090 -p 8091:8091 --restart always 3e2c26db9138
```