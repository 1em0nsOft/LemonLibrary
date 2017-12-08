### 下载镜像

```
docker pull registry.docker-cn.com/library/rabbitmq:3-management
```

### 运行

```
docker run -p 4369:4369 -p 5671:5671 -p 25672:25672 -p 15672:15672 -p 5672:5672 --name rabbit-mq -e RABBITMQ_DEFAULT_USER=1iURI -e RABBITMQ_DEFAULT_PASS=1em0nsOft+ -d rabbitmq:3-management
```



