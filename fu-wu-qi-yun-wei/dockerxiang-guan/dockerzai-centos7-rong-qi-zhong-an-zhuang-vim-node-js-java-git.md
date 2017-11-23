### 系统版本以及Docker版本查询

```
[root@localhost dockerImage]# cat /etc/redhat-release 
 CentOS Linux release 7.4.1708 (Core) 
[root@localhost dockerImage]# docker -v
 Docker version 17.09.0-ce, build afdb6d4
```

---

### 运行Docker 镜像

```
[root@localhost dockerImage]# docker images
REPOSITORY                              TAG                 IMAGE ID            CREATED             SIZE
registry.docker-cn.com/library/centos   7                   d123f4e55e12        4 days ago          197MB
[root@localhost dockerImage]# docker run -ti d123f4e55e12
[root@887006dcf961 /]#
[root@887006dcf961 /]# cat /etc/redhat-release
CentOS Linux release 7.4.1708 (Core)

```

### 安装 Vim

```
[root@887006dcf961 /]# yum install -y vim

```

### 安装软件常见问题

```
[root@887006dcf961 /]# curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -
......
Error executing command, exiting
[root@887006dcf961 /]#
# 解决方案
[root@887006dcf961 /]# vim /etc/yum.conf
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
debuglevel=2
logfile=/var/log/yum.log
exactarch=1
obsoletes=1
gpgcheck=1
plugins=1
installonly_limit=5
bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23
&
ref=http://bugs.centos.org/bug_report_page.php?category=yum
distroverpkg=centos-release
override_install_langs=en_US.utf8
# tsflags=nodocs 将这一行注释

```

### 安装Node.js

    [root@887006dcf961 /]# curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -
    ......
    ## Run `yum install -y nodejs` (as root) to install Node.js 6.x and npm.
    ## You may also need development tools to build native addons:
    ##   `yum install -y gcc-c++ make`
    [root@887006dcf961 /]#
    [root@887006dcf961 /]# yum install -y nodejs
    [root@887006dcf961 /]#
    [root@887006dcf961 /]# yum install -y gcc-c++ make
    [root@887006dcf961 /]#
    [root@887006dcf961 /]# node -v
    v6.12.0
    [root@887006dcf961 /]# npm -v
    3.10.10
    [root@887006dcf961 /]#


### 安装OpenJDK

```
[root@887006dcf961 /]# yum install -y java-1.8.0-openjdk.x86_64
[root@887006dcf961 /]#
[root@887006dcf961 /]# java -version
openjdk version "1.8.0_151"
OpenJDK Runtime Environment (build 1.8.0_151-b12)
OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)
[root@887006dcf961 /]#

```

### 安装Git

```
[root@887006dcf961 /]# yum install -y git
[root@887006dcf961 /]#
[root@887006dcf961 /]# git --version
git version 1.8.3.1
[root@887006dcf961 /]#

```

### 退出在进入Docker 镜像

```
[root@0766d39cd369 /]# node -v
bash: node: command not found
[root@0766d39cd369 /]# npm -v
bash: npm: command not found
[root@0766d39cd369 /]# git -v
bash: git: command not found
[root@0766d39cd369 /]# java -version
bash: java: command not found
[root@0766d39cd369 /]#

```

**哈哈小伙伴们惊呆了吧! 刚才的努力都没有了!!!, 别害怕先分析问题.**

```
 # 这个命令目前是, 在容器中运行一个新的镜像, 并进入到这个容器中, 所以每次都会创建一个新的容器
[root@localhost dockerImage]# docker run -ti d123f4e55e12

```

**查看所有容器, 你会发现已经存在两个容器, 所以只要进入之前的容器就可以了.**

```
[root@localhost dockerImage]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                           PORTS               NAMES
0766d39cd369        d123f4e55e12        "/bin/bash"         33 minutes ago      Exited (127) 11 minutes ago                          happy_noether
887006dcf961        d123f4e55e12        "/bin/bash"         About an hour ago   Exited (130) 35 minutes ago                          frosty_bhaskara
# 查看容器的PID
[root@localhost dockerImage]# docker inspect --format {{.State.Pid}} 887006dcf961
0 # 容器未启动
[root@localhost dockerImage]# docker start 887006dcf961
887006dcf961
[root@localhost dockerImage]# docker inspect --format {{.State.Pid}} 887006dcf961
22976
# 进入这个容器
[root@localhost dockerImage]# nsenter -t 22976 --mount --uts --ipc --net --pid

```

### 合并进入容器的shell语句

**nsenter –mount –uts –ipc –net –pid -t $\(docker inspect –format {{.State.Pid}} CONTAINER ID\)**

```
[root@localhost dockerImage]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
532744324fcc        d123f4e55e12        "/bin/bash"         36 minutes ago      Up 8 minutes                            blissful_mccarthy
[root@localhost dockerImage]#
[root@localhost dockerImage]# nsenter --mount --uts --ipc --net --pid -t $(docker inspect --format {{.State.Pid}} 532744324fcc)
[root@532744324fcc /]#

```

---

### 创建启动脚本

```
[root@532744324fcc /]# pwd
/home/bigdata
[root@532744324fcc /]# vim start.sh

```

加入如下代码

```
#!/bin/bash
source ~/.bashrc
cd /home/bigdata/client
npm start
cd ~

```

修改访问权限

```
[root@532744324fcc /]# chmod +x /home/bigdata/start.sh

```

测试执行脚本

```
 [root@532744324fcc /]# source /home/bigdata/start.sh


>
 client@1.0.0 start /home/bigdata/client

>
 nuxt start

 OPEN  http://172.17.0.2:3000

```

**172.17.0.2本机的IP地址,**

**`因为Nuxt.js 框架映射的问题, 必须要这么指定`**

**, 启动其它项目不需要指定本机IP地址**

\]

&gt;

