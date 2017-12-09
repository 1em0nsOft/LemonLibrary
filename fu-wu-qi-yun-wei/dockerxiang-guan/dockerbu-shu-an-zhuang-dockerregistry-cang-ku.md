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

### 登录docker registry
```
docker login docker.lemonlib.com
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


### nginx反向代理配置
```
server {
        listen  80;
        listen  443 ssl;
        server_name  docker.lemonlib.com;
        ssl on;
        ssl_certificate /etc/nginx/custom_conf/ssl/1_docker.lemonlib.com_bundle.crt;
        ssl_certificate_key /etc/nginx/custom_conf/ssl/2_docker.lemonlib.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
        ssl_prefer_server_ciphers on;

    location / {
        proxy_set_header X-Forwarded-Proto https;

        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass http://yourip:5000/;
        proxy_redirect off;
    }
}
```


### 错误解决
- 将nginx的http和https同时反向代理到docker服务器之后，docker login已经成功，但是docker push的时候仍然提示需要授权的话，那么在docker的nginx代理配置中加入如下：
```
proxy_set_header X-Forwarded-Proto https;
```