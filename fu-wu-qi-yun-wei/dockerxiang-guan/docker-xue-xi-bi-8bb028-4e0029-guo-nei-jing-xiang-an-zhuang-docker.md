### Docker 中国官方镜像加速官网

https://www.docker-cn.com/

### 下载社区版 \(Docker CE\)

https://docs.docker.com/engine/installation/linux/docker-ce/centos/

### 卸载旧版本\(Uninstall old versions\)

```
[root@localhost ~]# sudo yum remove docker \
                   docker-common \
                   docker-selinux \
                   docker-engine

```

### 使用存储库进行安装

#### **1. 安装所需的软件包 yum-utils提供了yum-config-manager 效用，并device-mapper-persistent-data和lvm2由需要 devicemapper存储驱动程序。**

```
[root@localhost ~]# sudo yum install -y yum-utils \
                   device-mapper-persistent-data \
                   lvm2

```

#### **2.使用以下命令来设置稳定的存储库。**

```
[root@localhost ~]# sudo yum-config-manager \
                   --add-repo \
                   https://download.docker.com/linux/centos/docker-ce.repo

```

#### **\(第一种, 默认安装最新版本\)安装最新版本的Docker CEDocker CE \(Install Docker CE\)**

```
[root@localhost ~]# sudo yum install docker-ce

```

#### **\(另一种, 自己选择安装的版本\) 在生产系统上，您应该安装特定版本的Docker CE，而不是始终使用最新版本。列出可用的版本。**

**此示例使用该sort -r命令按版本号从最高到最低排序结果**

```
[root@localhost docker]# yum list docker-ce --showduplicates | sort -r
docker-ce.x86_64            17.09.0.ce-1.el7.centos            docker-ce-stable
docker-ce.x86_64            17.09.0.ce-1.el7.centos            @docker-ce-stable
docker-ce.x86_64            17.06.2.ce-1.el7.centos            docker-ce-stable
docker-ce.x86_64            17.06.1.ce-1.el7.centos            docker-ce-stable
docker-ce.x86_64            17.06.0.ce-1.el7.centos            docker-ce-stable
docker-ce.x86_64            17.03.2.ce-1.el7.centos            docker-ce-stable
docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable
docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable

```

## 启动Docker

```
[root@localhost docker]# systemctl start docker
[root@localhost docker]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since 二 2017-11-07 15:13:54 CST; 1s ago
     Docs: https://docs.docker.com
 Main PID: 14873 (dockerd)
   Memory: 21.6M
   CGroup: /system.slice/docker.service
           ├─14873 /usr/bin/dockerd
           └─14884 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --metrics-inter...

```

## 添加自启动

```
[root@localhost docker]# systemctl enable docker
```



