### 下载镜像

```
docker pull registry.docker-cn.com/library/mongo
```

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

### MongoDB用户权限

内建的角色

1. 数据库用户角色：read、readWrite;
2. 数据库管理角色：dbAdmin、dbOwner、userAdmin；
3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
4. 备份恢复角色：backup、restore；
5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
6. 超级用户角色：root // 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
7. 内部角色：\_\_system

> 角色说明：  
> **Read**：允许用户读取指定数据库  
> **readWrite**：允许用户读写指定数据库  
> **dbAdmin**：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile  
> **userAdmin**：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户  
> **clusterAdmin**：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。  
> **readAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的读权限  
> **readWriteAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的读写权限  
> **userAdminAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限  
> **dbAdminAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。  
> **root**：只在admin数据库中可用。超级账号，超级权限

### 副本集形式部署

如果你想单点连接，那么从这里往下不需要看了，如果你想搭建MongoDB副本集，那么请移除MongoDB的所有docker容器，重新按照下面的方式启动容器和设置

#### 0. 创建用于auth的keyfile

```
mkdir -p /data/mongodb0_conf
cd /data/mongodb0_conf
openssl rand -base64 741 > mongodb-keyfile  
chmod 600 mongodb-keyfile
chown 999 mongodb_keyfile
```

#### 1. 启动三个mongodb进程 {#1-启动三个mongodb进程}

```
docker run --name mongodb-server0 \
    --restart always \
    -v /data/mongodb0:/data/db \
    -v /data/mongodb0_conf:/opt/keyfile \
    -p 27017:27017 \
    -d d22 \
    --smallfiles \
    --keyFile /opt/keyfile/mongodb-keyfile \
    --replSet exuehui-mongo-set 

docker run --name mongodb-server1 \
    --restart always \
    -v /data/mongodb1:/data/db \
    -v /data/mongodb1_conf:/opt/keyfile \
    -p 27018:27017 \
    -d d22 \
    --smallfiles \
    --keyFile /opt/keyfile/mongodb-keyfile \
    --replSet exuehui-mongo-set

docker run --name mongodb-server0 \
    --restart always \
    -v /data/mongodb0:/data/db \
    -v /data/mongodb0_conf:/opt/keyfile \
    -p 37017:27017 \
    -d d22 \
    --smallfiles \
    --keyFile /opt/keyfile/mongodb-keyfile \
    --replSet exuehui-mongo-set
```

#### 2 进入 mongodb docker {#2-进入-mongodb-docker}

```
docker run -it  --name mongo-client mongo /bin/bash
```

#### 3 进入要作为master数据库的 mongodb shell {#3-进入要作为master数据库的-mongodb-shell}

```
mongo 192.168.31.206:27017/admin
```

#### 4 初始化副本集, \_id和启动时设置的replSet参数相同 {#4-初始化副本集-id和启动时设置的replset参数相同}

```
rs.initiate({ _id:"exuehui-mongo-set", members:[
{_id:0,host:"home.lemonsoft.vip:27017"}, {_id:1,host:"home.lemonsoft.vip:27018"}, {_id:2,host:"home.lemonsoft.vip:37017"}
]})
```

#### 5 查看副本集状态 {#5-查看副本集状态}

```
rs.status()
```

#### 6.创建权限用户

```
use admin;
db.createUser({ user: '1iURI', pwd: 'rootroot', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });

use exuehui
db.createUser({ user: '1iURI-exuehui', pwd: 'rootroot', roles: [ { role: "readWrite", db: "exuehui" } ] });
```

#### 7. 重新运行容器，并添加--auth

```
docker run --name mongodb-server0 \
    --restart always \
    -v /data/mongodb0:/data/db \
    -v /data/mongodb0_conf:/opt/keyfile \
    -p 27017:27017 \
    -d d22 \
    --smallfiles \
    --keyFile /opt/keyfile/mongodb-keyfile \
    --auth \
    --replSet exuehui-mongo-set 

docker run --name mongodb-server1 \
    --restart always \
    -v /data/mongodb1:/data/db \
    -v /data/mongodb1_conf:/opt/keyfile \
    -p 27018:27017 \
    -d d22 \
    --smallfiles \
    --keyFile /opt/keyfile/mongodb-keyfile \
    --auth \
    --replSet exuehui-mongo-set

docker run --name mongodb-server0 \
    --restart always \
    -v /data/mongodb0:/data/db \
    -v /data/mongodb0_conf:/opt/keyfile \
    -p 37017:27017 \
    -d d22 \
    --smallfiles \
    --keyFile /opt/keyfile/mongodb-keyfile \
    --auth \
    --replSet exuehui-mongo-set
```

> 参考资料：
>
> [http://tcrct.iteye.com/blog/2108099](http://tcrct.iteye.com/blog/2108099)



