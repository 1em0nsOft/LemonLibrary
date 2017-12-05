### 下载镜像

```
docker pull registry.docker-cn.com/library/mongo
```

### 

### 创建本地数据文件夹

```
mkdir /data/mongodb0
```



### 启动MongoDB容器

```
docker run --name mongodb-server0 -v /data/mongodb0:/data/db -p 27017:27017 -d 镜像ID --auth
```

* -v后面的参数表示把数据文件挂载到宿主机的路径
* -p把mongo端口映射到宿主机的指定端口
* --auth表示连接mongodb需要授权

### 为MongoDB添加管理员用户

* 进入MongoDB控制台

```
docker exec -it some-mongo mongo admin
```

* 添加用户命令

```
db.createUser({ user: '1iURI', pwd: 'rootroot', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });
```



