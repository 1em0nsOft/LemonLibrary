### 准备

MySQL数据库，本文中只用到两台，一台主，一台从，多台的原理和两台一致，就是重复配置的过程

为了方便我直接使用Docker在两台服务器上分别MySQL，A机器使用`3306`端口作为主，B机器使用`33306`端口作为从。



分别在两台机器上安装Docker后首先pull官方MySQL镜像：

```
docker pull registry.docker-cn.com/library/mysql:5.7
```

然后分别在AB两台机器上新建两个文件夹`/data/mysql0`和`/data/mysql0_conf`作为数据存储文件夹和配置文件存储文件夹：

```
mkdir /data/mysql0 /data/mysql0_conf
```

接下来Docker run就可以了：

> 注意：最后的5709795eeffa为镜像ID，你需要先使用docker images查看你本地的镜像ID，然后在命令中使用你本地的镜像ID

A机器 - 3306：

```
docker run -p 3306:3306 --restart always -v /data/mysql0:/var/lib/mysql -v /data/mysql0_conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=rootroot -d 5709795eeffa
```

B机器 - 33306：

```
docker run -p 33306:3306 --restart always -v /data/mysql0:/var/lib/mysql -v /data/mysql0_conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=rootroot -d 5709795eeffa
```

这样，两台MySQL服务器就搭建完毕，默认账号root，默认密码rootroot



### 配置Master

> 再次提示：我使用的是3306那台A机器作为Master

在A机器宿主机上的/data/mysql0_conf文件夹中新建MySQL配置文件my.cnf，然后使用vim编辑其内容：

```
touch my.cnf
vim my.cnf
```

在其中配置MySQL的设置，以下是我的配置文件，其中###标注的部分才是涉及到主从相关的，其他部分用户可以忽略：

```
[mysqld]

character-set-server=utf8
collation-server=utf8_bin
default-storage-engine=INNODB
max_allowed_packet=256M
innodb_log_file_size=2GB
binlog_format=row
transaction-isolation=READ-COMMITTED

######## MySQL主从配置相关，上面的用户可以忽略，但是需要第一行[mysqld] ########

server-id=3306
log-bin=mysql-bin
binlog-do-db=testgogo
binlog-ignore-db=mysql
binlog-ignore-db=information_schema
binlog-ignore-db=performance_schema
binlog-ignore-db=sys
expire_logs_days=7

######## MySQL主从配置相关 END  #######
```

#### 配置说明

- server\_id : 服务器ID，多台MySQL的ID不能一致，在这里我用端口号作为ID，你也可以换成其他数字
- log-bimysql-bin : 开启bin-log日志
- binlog-do-db=testgoo : 要同步的库，不配置的话则所有库都进行同步
- binlog-ignore-db=xxx : 不要同步的库，建议将MySQL自带的库忽略同步，mysql、information_schema
、performance_schema和sys四个库，每个占一行
- expire_logs_days=7 本次保存log的时间为7天，根据你的情况设置

#### 重启MySQL服务

首先查看MySQL容器ID
```
docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS                    NAMES
10e9151d1ed6        5709795eeffa        "docker-entrypoint..."   3 hours ago         Up 3 hours                  0.0.0.0:3306->3306/tcp   youthful_lamarr
```
重启容器
```
docker restart 10e9151d1ed6
```
查看Master配置结果，使用命令行连接至MySQL，执行命令：
```
mysql> show variables like '%log_bin%';
+---------------------------------+--------------------------------+
| Variable_name                   | Value                          |
+---------------------------------+--------------------------------+
| log_bin                         | ON                             |
| log_bin_basename                | /var/lib/mysql/mysql-bin       |
| log_bin_index                   | /var/lib/mysql/mysql-bin.index |
| log_bin_trust_function_creators | OFF                            |
| log_bin_use_v1_row_events       | OFF                            |
| sql_log_bin                     | ON                             |
+---------------------------------+--------------------------------+
6 rows in set (0.04 sec)
```
显示log_bin  ON说明成功，接下来创建一个用于同步数据的用户，用户名backup，密码123456


```
grant replication slave on *.* to 'backup'@'%' identified by '123456';
```

创建完毕后使用命令查看是否创建成功
```
mysql> use mysql
mysql> select user,authentication_string,host from user;
```


### 配置Slave库

#### 首先将Slave库中的数据与Master中的数据同步，用工具导出sql然后同步或直接导出SQLdump备份然后再恢复都可以，同步方式这里不多赘述。

在B机器宿主机上的/data/mysql0_conf文件夹中新建MySQL配置文件my.cnf，然后使用vim编辑其内容：

```
touch my.cnf
vim my.cnf
```

在其中配置MySQL的设置，以下是我的配置文件，其中###标注的部分才是涉及到主从相关的，其他部分用户可以忽略：

```
[mysqld]

character-set-server=utf8
collation-server=utf8_bin
default-storage-engine=INNODB
max_allowed_packet=256M
innodb_log_file_size=2GB
binlog_format=row
transaction-isolation=READ-COMMITTED


######## MySQL主从配置相关，上面的用户可以忽略，但是需要第一行[mysqld] ########

server_id=33306
read_only = 1
master_info_repository=TABLE
relay_log_info_repository=TABLE
#relay_log_recovery=1
#super_read_only=1

######## MySQL主从配置相关 END #############
```

```
change master to 
    master_host='192.168.31.206',
    master_port=3306,
    master_user='backup',
    master_password='1em0nsOft+backup',
    master_log_file='mysql-bin.000001',
    master_log_pos=154;
```

