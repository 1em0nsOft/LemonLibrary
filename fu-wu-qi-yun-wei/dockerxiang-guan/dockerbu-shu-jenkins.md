### 下载镜像

```
docker pull registry.docker-cn.com/jenkins/jenkins
```

### 运行Jenkins容器
```
docker run -p 20000:8080 -p 20001:50000 --restart always -v /data/data/jekins:/var/jenkins_home -v /data/data/common:/common -d registry.docker-cn.com/jenkins/jenkins
```

