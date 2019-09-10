---
title: CentOS7中Docker-ce的卸载和安装
tags:
  - CentOS7
  - Docker
categories:
  - 技术Tech
date: 2019-08-18 23:46:14
---
### 1.查看电脑里是否已经安装docker软件包
``` bash
$ sudo yum list installed | grep docker
docker-client.x86_64                 2:1.13.1-63.git94f4240.el7.centos @extras
docker-common.x86_64                 2:1.13.1-63.git94f4240.el7.centos @extras
```
### 2. 卸载不需要的docker软件包
``` bash
$ sudo yum -y remove docker-client.x86_64
$ sudo yum -y remove docker-common.x86_64
```
### 3. 删除所有的镜像、容器、数据卷、配置文件等
``` bash
$ sudo rm -rf /var/lib/docker
```
PS:此时可能会报错, 在文末展示常见错误和解决方案.
### 4. 安装新的需要的docker-ce, 可以安装最新的, 也可以安装指定版本. [官方安装指南](https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository) 
安装最新的:
``` bash
$ sudo yum install docker-ce docker-ce-cli containerd.io
```
安装指定版本:
``` bash
$ yum list docker-ce --showduplicates | sort -r
docker-ce.x86_64  3:18.09.1-3.el7                     docker-ce-stable
docker-ce.x86_64  3:18.09.0-3.el7                     docker-ce-stable
docker-ce.x86_64  18.06.1.ce-3.el7                    docker-ce-stable
docker-ce.x86_64  18.06.0.ce-3.el7                    docker-ce-stable
```
从中选择自己想要的版本. The list returned depends on which repositories are enabled, and is specific to your version of CentOS (indicated by the .el7 suffix in this example).

b. Install a specific version by its fully qualified package name, which is the package name (docker-ce) plus the version string (2nd column) starting at the first colon (:), up to the first hyphen, separated by a hyphen (-). For example, docker-ce-18.09.1.
``` bash
$ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```
### 5. 开始docker.
``` bash
$ sudo systemctl start docker
```
### 6. 测试docker.
``` bash
$ sudo docker run hello-world
```
### 7. 可选,把当前user加入docker group使之具有root权限.[官方指导](https://docs.docker.com/install/linux/linux-postinstall/)
Post-installation steps for Linux
This section contains optional procedures for configuring Linux hosts to work better with Docker.
Manage Docker as a non-root user
The Docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user root and other users can only access it using sudo. The Docker daemon always runs as the root user.
If you don’t want to preface the docker command with sudo, create a Unix group called docker and add users to it. When the Docker daemon starts, it creates a Unix socket accessible by members of the docker group.
创建docker group并添加your user:
``` bash
$ sudo groupadd docker # 创建docker group.
$ sudo usermod -aG docker $USER # 添加你的user到docker group里.
```
登出再登入, 以使得你的group membership重设了.注意, 如果是虚拟机,也许需要重启虚拟机. 桌面Linux系统,则完全登出,然后再登入.
在Linux里, 你也可以运行下面命令行去激活gtoups的改变:
``` bash
$ newgrp docker 
```
确认你能够不用sudo运行docker命令:
``` bash
$ docker run hello-world
```

## 常见报错
### 1. 卸载docker-ce时, 出现如下报错:
``` bash
Failed to set locale, defaulting to C
```
解决方案:
You can see this often in CentOS because you never set the locale for your Linux.
输入如下命令:
``` bash
echo "export LC_ALL=en_US.UTF-8" >> /etc/profile
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
export LANGUAGE=en_US.UTF-8
export LC_COLLATE=C
export LC_CTYPE=en_US.UTF-8
```
然后, 断开bash,再重联bash.
### 2. 卸载docker-ce后, 尝试删除镜像、容器和存储卷(sudo rm -rf /var/lib/docker/), 可能报错如下:
``` bash
failed to remove `/var/lib/docker': Device or resource busy
```
可能的原因是没有umount. 使用如下命令:
``` bash
$ cat /proc/mounts |grep docker # 显示docker mount point.
$ sudo umount /path # 逐条umonut上述显示的/path. 注意是所有!尤其注意最初一行,容易忽略.
``` 
然后,
``` bash
$ sudo rm -rf /var/lib/docker/
```
### 3. 上述之后,如果仍然显示:
``` bash
$ systemctl start docker
Job for docker.service failed because the control process exited with error code. See "systemctl status docker.service" and "journalctl -xe" for details.
```
使用如下命令:
``` bash
$ journalctl -u docker
```
查看log, 根据提示操作! 我遇到的情况是需要delete docker.pid.
