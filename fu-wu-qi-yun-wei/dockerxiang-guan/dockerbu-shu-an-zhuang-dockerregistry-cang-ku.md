### 下载镜像
```
docker pull registry.docker-cn.com/library/registry
```

### 创建本地数据文件夹
```
mkdir /data/data/docker-registry
```

### 运行容器
```
docker run -d -p 5000:5000 --restart=always \  
  -v /data/data/docker-registry:/var/lib/registry/ \
  registry.docker-cn.com/library/registry
```

### 验证是否运行成功
```
curl http://192.168.0.100:5000/v2/_catalog
```
返回{"repositories":[]}说明正常

