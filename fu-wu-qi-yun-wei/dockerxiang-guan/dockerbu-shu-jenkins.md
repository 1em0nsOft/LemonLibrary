### 下载镜像

```
docker pull registry.docker-cn.com/jenkins/jenkins
```

### 创建数据文件夹
```
mkdir /data/data/jekins
mkdir /data/data/common
```
> 我们在/data/data/common中把jdk和maven等应用放进去

### 运行Jenkins容器
```
docker run -p 20000:8080 -p 20001:50000 --restart always -v /data/data/jekins:/var/jenkins_home -v /data/data/common:/common -d registry.docker-cn.com/jenkins/jenkins
```

