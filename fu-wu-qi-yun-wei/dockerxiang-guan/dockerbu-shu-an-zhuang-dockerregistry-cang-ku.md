### 下载镜像
```
docker pull registry.docker-cn.com/library/registry
```

### 创建本地数据文件夹
```
mkdir /data/data/docker-registry
mkdir /data/data/docker-registry-auth
```

### 创建用户授权文件
```
docker run --entrypoint htpasswd registry.docker-cn.com/library/registry -Bbn 1iURI root  >> /data/data/docker-registry-auth/htpasswd 
```

### 运行容器
```
docker run -d -p 5000:5000 --restart=always \  
  -v /data/data/docker-registry-auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \  
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
  -v /data/data/docker-registry:/var/lib/registry/ \
  registry.docker-cn.com/library/registry
```

### 验证是否运行成功
```
curl http://192.168.0.100:5000/v2/_catalog
```
返回{"repositories":[]}说明正常


### 安装docker-registry-ui
```
docker run \
  -d \
  --restart always \
  -e ENV_DOCKER_REGISTRY_HOST=docker.lemonlib.com \
  -e ENV_DOCKER_REGISTRY_PORT=443 \
  -e ENV_DOCKER_REGISTRY_USE_SSL=1 \
  -p 5001:80 \
  registry.docker-cn.com/konradkleine/docker-registry-frontend
```



### 错误解决
- 将nginx的http和https同时反向代理到docker服务器之后，docker login已经成功，但是docker push的时候仍然提示需要授权的话，那么在docker的nginx代理配置中加入如下：
```
proxy_set_header X-Forwarded-Proto https;
```