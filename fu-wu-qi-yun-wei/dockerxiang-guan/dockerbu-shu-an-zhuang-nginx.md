### 下载镜像

```
docker pull registry.docker-cn.com/library/nginx
```

### 准备数据

```
mkdir /data/data/nginx # 存储所有nginx相关数据
mkdir /data/data/nginx/conf # 存储nginx的扩展配置数据
mkdir /data/data/nginx/html #存储nginx的html数据


$ docker run --name tmp-nginx-container -d nginx
$ docker cp tmp-nginx-container:/etc/nginx/nginx.conf /data/data/nginx/nginx.conf
$ docker rm -f tmp-nginx-container
```

### 运行容器

```
docker run --name nginx-server -p 80:80 -v /data/data/nginx/conf:/etc/nginx/custom_conf -v /data/data/nginx/nginx.conf:/etc/nginx/nginx.conf:ro  -v /data/data/nginx/html:/usr/share/nginx/html:ro -d 9e7424e5dbae
```

### 配置nginx
- 配置nginx.conf

```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/custom_conf/*.conf;
    ## 加入配置文件扫描目录

}
```

##### 重点是配置文件中最后一行 加入配置文件扫描目录

```
include /etc/nginx/custom_conf/*.conf;

```

- 在/data/data/nginx/conf中添加反向代理配置文件
```
server {
    listen       80;
    server_name  git.*;

    location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-Ip $remote_addr;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass http://118.89.232.188:7990/;
        proxy_redirect off;
    }
}
```

### 重启nginx即可

```
docker restart 59d49622523c
```

