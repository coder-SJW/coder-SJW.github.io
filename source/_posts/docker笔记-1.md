---
title: docker笔记
date: 2021-12-31 01:46:49
tags: 
 - docker
categories: Docker
cover: https://i.loli.net/2021/02/22/pnbmUDYPf4tvHjh.png
---

# 一、Docker基本组成

![点击查看源网页](https://gitee.com/coder-SJW/blogimg/raw/master/img/6F54XuHbN8aUG1D.png)

**镜像（image）：**

docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，通过这个镜像可以创建多个容器（最终项目运行就是在容器中）。

**容器（container）：**

Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建。

容器基本命令=》启动、停止、删除

**仓库（repository）：**

仓库就是存放镜像的地方

仓库分为共有仓库和私有仓库

Docker Hub（默认是国外的仓库）

阿里云镜像加速器（配置镜像加速=》常用！！）

# 二、安装Docker

## 1、安装步骤

[docker官方安装指南(centos)](https://docs.docker.com/engine/install/centos/)

```shell
# 1、卸载旧的docker版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2、需要的安装包
yum install -y yum-utils
# 3、设置镜像的仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo #默认的是国外的！！
yum-config-manager \
	--add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo #常用阿里云的镜像仓库
# 4、更新yum软件包索引
yum makecache fast
# 5、安装docker相关组件（默认最新版）
yum install docker-ce docker-ce-cli containerd.io
# 6、启动docker
systemctl start docker
# 7、检查是否安装成功
docker version
```

![image-20210219092510818](https://gitee.com/coder-SJW/blogimg/raw/master/img/6F54XuHbN8aUG1D.png)

```shell
# 8、运行hello world
docker run hello-world
```

![image-20210219092724962](https://gitee.com/coder-SJW/blogimg/raw/master/img/6F54XuHbN8aUG1D.png)

```shell
# 9、查看docker镜像(mysql\redis是之前拉的镜像、按步骤现在只有hello-world的镜像)
docker images
```

![image-20210219093539353](https://gitee.com/coder-SJW/blogimg/raw/master/img/6F54XuHbN8aUG1D.png)

## 2、回顾hello-world流程

上面步骤中运行了hello-word镜像，执行docker run之后发生了什么？

![image-20210219100047585](https://gitee.com/coder-SJW/blogimg/raw/master/img/6F54XuHbN8aUG1D.png)

## 3、docker底层原理

> docker是怎么工作的？

docker是一个client-server结构的系统，docker的守护进程运行在主机上。通过socket从客服端访问！

dockerServer接收到Docker-Client的指令，就会执行这个命令！

![image-20210219101422288](https://i.loli.net/2021/02/22/V1g3dE5uSD6bFWt.png)

> docker为什么比虚拟机快？

1、docker有着比虚拟机更少的抽象层

2、docker利用的是宿主机的内核，vm需要的是guest OS

![image-20210224182401352](https://i.loli.net/2021/02/24/NBrUb8WVMOeLYv5.png)

>  所有说，新建一个容器的时候，docker不需要像虚拟机一样加载一个操作系统内核，避免引导。虚拟机是加载guestOs，分钟级别的；而docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级！

![image-20210219102708472](https://i.loli.net/2021/02/22/WutS1CdoXcg9kiK.png)



# 三、阿里云镜像加速

登陆阿里云：控制台=》产品与服务=》容器镜像服务=》镜像加速器

选择对应的操作系统

![image-20210219095101195](https://i.loli.net/2021/02/22/TQy23u7VlzwmLRE.png)

![image-20210219095150647](https://i.loli.net/2021/02/22/lY81sNFLfdhAP3U.png)

```shell
#配置命令：这里是centos，版本要对应
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://hfvn90en.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

# 四、Docker常用命令

## 1、帮助命令

```shell
docker version #显示docker版本信息
docker info #显示docker的系统信息、包括镜像和容器的数量
docker 命令 --help #帮助命令
```

帮助文档：[官方文档](https://docs.docker.com/reference/)

## 2、镜像命令

### 查看本地镜像

docker images

```shell
[root@SJW-aliyun ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
mysql         5.7       a70d36bc331a   4 weeks ago     449MB
mysql         latest    c8562eaf9d81   4 weeks ago     546MB
redis         latest    621ceef7494a   5 weeks ago     104MB
hello-world   latest    bf756fb1ae65   13 months ago   13.3kB

# 接收
REPOSITORY 镜像的仓库源
TAG		   镜像的标签
IMAGE ID   镜像的id
CREATED	   镜像创建的时间
SIZE       镜像的大小

#命令可选项
-a, --all           #列出所有镜像
-q, --quiet         #只显示镜像的id
```

### 搜索镜像

docker search

```shell
[root@SJW-aliyun ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10520     [OK]       
mariadb                           MariaDB is a community-developed fork of MyS…   3928      [OK]       

# 可选项，进行过滤搜索
--filter=stars=3000 #搜索出来的镜像就是stars大于3000的
[root@SJW-aliyun ~]# docker search mysql --filter=stars=4000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10520     [OK]     
```

### 下载镜像

docker pull

```shell
# 下载镜像 docker pull 镜像名[:tag]	==》  eg: docker pull tomcat:8.5
[root@SJW-aliyun ~]# docker pull tomcat
Using default tag: latest #如果不写tag，默认就是latest
latest: Pulling from library/tomcat
b9a857cbf04d: Pull complete 	#分层下载，docker images的核心 联合文件系统
d557ee20540b: Pull complete 	#如果下载该镜像的其它版本可以共用相同的层，不需要重复下载，极大地节省空间
3b9ca4f00c2e: Pull complete 
667fd949ed93: Pull complete 
661d3b55f657: Pull complete 
511ef4338a0b: Pull complete 
a56db448fefe: Pull complete 
00612a99c7dc: Pull complete 
326f9601c512: Pull complete 
c547db74f1e1: Pull complete 
Digest: sha256:94cc18203335e400dbafcd0633f33c53663b1c1012a13bcad58cced9cd9d1305
Status: Downloaded newer image for tomcat:latest
docker.io/library/tomcat:latest
```

### 删除镜像 

docker rmi

```shell
[root@SJW-aliyun ~]# docker rmi a70d36bc331a #删除指定镜像[root@SJW-aliyun ~]# docker rmi 镜像id 镜像id #删除多个镜像[root@SJW-aliyun ~]# docker rmi $(docker images -aq) #删除多个镜像
```



## 3、容器命令

> 有了镜像才能 创建容器

下载一个centos镜像来测试学习

```shell
[root@SJW-aliyun ~]# docker pull centosUsing default tag: latestlatest: Pulling from library/centos7a0437f04f83: Pull complete Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1Status: Downloaded newer image for centos:latestdocker.io/library/centos:latest
```

### ==**新建容器**==并启动

docker run

```shell
docker run [可选参数] image#可选参数说明--name="name"	容器别名，用来区分容器-d				后台方式交互-it				使用交互方式运行，加入容器查看内容-p				指定容器端口 -p 主机端口:容器端口-P				随机指定端口#测试，启动并进入容器[root@SJW-aliyun ~]# docker run -it centos /bin/bash[root@cd7a513e67b9 /]# ls	#查看容器内的centos，其为基础版本，很多命令不完善bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var#从容器中退回主机[root@cd7a513e67b9 /]# exitexit
```

### 列出所有运行的容器

docker ps

```shell
# docker ps 命令	# 列出当前正在运行的容器-a	# 列出当前正在运行的容器+历史运行过的容器-n=？#显示最近创建的容器-q	#只显示容器的编号[root@SJW-aliyun ~]# docker psCONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES[root@SJW-aliyun ~]# docker ps -aCONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                      PORTS     NAMEScd7a513e67b9   centos    "/bin/bash"              7 minutes ago   Exited (0) 5 minutes ago              hopeful_chaplygine27753e62511   redis     "docker-entrypoint.s…"   11 days ago     Exited (0) 59 minutes ago             redis
```

### 删除容器

docker rm

```shell
docker rm 容器id					#删除指定容器，不能删除正在运行的容器，如果要强制删除 rm -fdocker rm -f $(docker ps -a)	#删除所有容器docker ps -a|xargs docker rm	#删除所有容器
```

### 启动和停止容器操作

```shell
docker start 容器id	#启动容器docker restart 容器id	#重启容器docker stop 容器id	#停止正在运行的容器docker kill 容器id	#强制停止当前容器
```

### 退出容器

```shell
exit	#直接容器停止并退出Ctrl + P + Q	#容器不停止退出
```

### 其它常用命令

后台启动容器（-d）

```shell
# 命令 docker run -d 镜像名[root@SJW-aliyun ~]# docker run -d centos#问题：运行docker ps，发现centos停止了#原因：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有运用就会自动停止。
```

查看日志

```shell
docker logs -tf --tail 条数 容器id
```

查看容器中的进程信息

```shell
docker top 容器id
```

查看镜像元数据

```shell
docker inspect 容器id
```

进入当前正在运行的容器

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器修改一些配置#命令（方式一）docker exec -it 容器id bashShell#测试[root@SJW-aliyun ~]# docker psCONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMESfe1741b3f823   centos    "/bin/bash"   17 seconds ago   Up 16 seconds             crazy_morse[root@SJW-aliyun ~]# docker exec -it fe1741b3f823 /bin/bash# 方式二docker attach 容器id#测试[root@SJW-aliyun ~]# docker psCONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMESfe1741b3f823   centos    "/bin/bash"   8 minutes ago   Up 8 minutes             crazy_morse[root@SJW-aliyun ~]# docker attach fe1741b3f823[root@fe1741b3f823 /]# # docker exec 进入容器开启一个新的终端，可以在里面操作（常用）# docker attach 进入容器正在执行的终端，不会启动新的进程
```

从容器内拷贝文件到主机上（宿主机）

```shell
#命令docker cp 容器id:容器内路径 宿主机路径#测试[root@SJW-aliyun sjw]# docker attach fe1741b3f823	进入docker容器[root@fe1741b3f823 /]# lsbin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var[root@fe1741b3f823 /]# cd /home[root@fe1741b3f823 home]# touch test.java	创建文件[root@fe1741b3f823 home]# lstest.java[root@fe1741b3f823 home]# exit	退出容器exit[root@SJW-aliyun sjw]# docker cp fe1741b3f823:/home/test.java /home/sjw	从容器内拷贝文件到主机上[root@SJW-aliyun sjw]# cd /home/sjw[root@SJW-aliyun sjw]# lstest.java[root@SJW-aliyun sjw]# # 拷贝是一个手动过程，未来可使用 -v 卷的技术。
```

## 小结

![image-20210222121826673](https://i.loli.net/2021/02/22/pnbmUDYPf4tvHjh.png)

# 五、练习

## 1、部署nginx

> 拉取nginx镜像，并启动。外部3344端口映射容器内部80端口，访问外部3344间接访问容器80端口。容器相对于小型linux系统

![image-20210222150709816](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210226104132151.png)

测试：访问3344端口

![image-20210222151024484](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210226104132151.png)

## 2、部署tomcat

> 拉取tomcat镜像，并启动。

![image-20210222152946214](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210226104132151.png)

测试：访问tomcat

![image-20210222153230316](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210226104132151.png)

==ps：访问结果为404，那是因为tomcat镜像不是完整的默认是最小镜像，不必要的就被剔除掉，保证最小可运行环境！如下==

![image-20210222153654975](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210226104132151.png)

# 六、Docker可视化

**什么是portainer？**

Docker图形化管理工具，提供一个后台面板供我们使用！

```shell
docker run -d -p 8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock \--privileged=true portainer/portainer
```

测试：外网访问8088，==记住要把阿里云安全组对应的端口号打开！！！==

![image-20210222160051724](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210226104132151.png)

创建一个用户、选择本地连接，下面就是可视化面板

![image-20210222160435296](https://i.loli.net/2021/02/22/txj94mIAPnrifEX.png)

# 六、commit镜像

```shell
docker commit 提交容器成为一个新的副本（镜像）相当于把原有的镜像经过自己的操作后封装成一个新的镜像# 此命令和git commit命令类似docker commit -m="描述信息" -a="作者" 容器id 目标镜像名[:TAG]
```

==测试==

```shell
# 1、启动一个默认的tomcat# 2、这个默认的tomcat是没有webapps运用（访问不了页面），镜像原因，官方的镜像默认webapps下面没有文件！# 3、将webapps.dist的文件拷贝进webapps里# 4、将操作过的容器通过commit提交为一个镜像！以后可直接使用修改过的镜像！！
```

启动步骤省略，见上文！

![image-20210224103550687](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210224103550687.png)

![image-20210224104052076](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210224103550687.png)

==如果想保存当前容器状态，就可以通过commit来提交，获得一个镜像==

# ==七、容器数据卷（重点）==

## 1、什么是容器数据卷？

> docker的理念回顾？

将应用和环境打包成一个镜像！

> 应用（容器）产生的数据该何去何从？

如果数据都在容器中，那么删除容器的同时，数据也会丢失

==需要：数据可以持久化==

> 什么是卷技术？

容器之间可以有一个数据共享的技术！把docker容器中产生的数据同步到本地！

这就是卷技术！目录挂载，将我们容器的目录挂载到linux（宿主机）上面！！

![image-20210224110608269](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210224103550687.png)

==总结一句话：容器的持久化和同步操作！容器间也是可以数据共享的！（多个容器挂载到宿主机同一目录）==

## 2、使用数据卷（通过命令）

> 方式一：直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录:容器内目录# 测试[root@SJW-aliyun home]# docker run -it -v /home/test:/home centos /bin/bash
```

![image-20210224111804641](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210224103550687.png)

使用 docker inspect命令查看容器详情

![image-20210224112226064](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210224103550687.png)

**测试文件的同步**

![image-20210224114052209](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210224103550687.png)

### 实战：安装mysql

==目标：mysql数据持久化==

```shell
# 1、启动一个mysql容器，需要做数据挂载！需要配置mysql密码！# 官方测试：docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag# -d后台运行 -p 端口映射 -e环境配置 -v卷挂载[root@SJW-aliyun /]# docker run -d -p 8082:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql
```

![image-20210224171915609](https://gitee.com/coder-SJW/blogimg/raw/master/img/dha91XtQnxuDANe.png)

```shell
# 2、在本地使用Navicat测试连接# 本地连接阿里云服务器8082端口映射mysql容器3306端口进行访问！记得打开阿里云对应的端口组！
```

![image-20210224172213411](https://gitee.com/coder-SJW/blogimg/raw/master/img/dha91XtQnxuDANe.png)

```shell
# 3、本地创建数据库# 看文件是否挂载成功？
```

![image-20210224172857460](https://gitee.com/coder-SJW/blogimg/raw/master/img/dha91XtQnxuDANe.png)

![image-20210224173045289](https://gitee.com/coder-SJW/blogimg/raw/master/img/dha91XtQnxuDANe.png)

> 将mysql容器删除，数据还在不在？

![image-20210224173736250](https://gitee.com/coder-SJW/blogimg/raw/master/img/dha91XtQnxuDANe.png)

==实践结果：mysql数据持久化成功！文件挂载成功！哈哈哈哈==

### 具名挂载和匿名挂载

```shell
# 匿名挂载-v 容器内路径docker run -d -P --name nginx01 -v /etc/nginx nginx# 查看所有的volume（卷）的情况[root@SJW-aliyun _data]# docker volume lsDRIVER    VOLUME NAMElocal     3eaa24893e76eec6766865aa607f01c6f7e82268383f76b637165bbe93253c64#这种就是匿名挂载，我们在-v只写了容器内的路径没有写容器外的路径！！#具名挂载-v 卷名:容器内路径docker run -d -P --name nginx02 -v nginx02:/etc/nginx nginx[root@SJW-aliyun _data]# docker volume lsDRIVER    VOLUME NAMElocal     nginx02
```

> 查看卷的详情

![image-20210224181006405](https://gitee.com/coder-SJW/blogimg/raw/master/img/dha91XtQnxuDANe.png)

所有的docker容器内的卷，没有指定目录的情况下(具名和匿名挂载)都是在==/var/lib/docker/volumes/xxx/_data==

通过具名挂载考研方便的找到我们的一个卷，大多数情况使用的是==具名挂载==

> 如何确定是具名挂载、匿名挂载还是指定路径挂载！

```shell
-v 容器内路径		#匿名挂载-v 卷名：容器内路径	  #具名挂载-v 宿主机 路径：容器内路径	#指定路径挂载
```

> 拓展：

```shell
# 通过 -v 容器内路径：ro rw 改变读写权限ro：readonly #只读rw：readwrite #可读可写（默认）#一旦设置了容器权限，容器对我们挂载出来的内容就有了限定！docker run -d -P --name nginx01 -v /etc/nginx:ro nginxdocker run -d -P --name nginx01 -v /etc/nginx:rw nginx# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内无法操作！！
```

## 3、使用数据卷（通过DockerFile）

> 方式二

==DoockerFile就是用来构建docker镜像的文件！ 实质是命令脚本！==

通过脚本可以生成镜像，镜像是一层一层的，脚本是一个一个的命令，每个命令都是一层！

```shell
#创建一个dockerfile文件，文件随意#文件中的内容：指令（大写）+参数FROM centosVOLUME ["volume1","volume2"]CMD echo "---end---"CMD /bin/bash#这里的每个命令都是镜像的一层！
```



![image-20210225093929054](https://gitee.com/coder-SJW/blogimg/raw/master/img/YRiNS41DXIpyEqv.png)

> 启动自己创建的镜像

不清楚为什么这里挂载成了一个目录（狗头），问题不大

![image-20210225095454198](https://gitee.com/coder-SJW/blogimg/raw/master/img/YRiNS41DXIpyEqv.png)

**这里的挂载肯定是个匿名挂载，只指定了容器内路径**

![image-20210225095804926](https://gitee.com/coder-SJW/blogimg/raw/master/img/YRiNS41DXIpyEqv.png)

==这个卷和外部肯定有一个同步的目录==

> 通过docker inspect命令查看容器详情，可查看挂载路径

![image-20210225100428286](https://gitee.com/coder-SJW/blogimg/raw/master/img/YRiNS41DXIpyEqv.png)

==总结：==

​	==使用dockerfile来挂载数据在未来使用得十分多，因为我们通常会构建自己的镜像，如果构建镜像时没有挂载卷，要手动镜像挂载 -v 容器外路径:容器内路径！！==

## 4、数据卷容器

> 数据卷容器解决什么问题？

**解决多个容器之间数据同步问题**

# 八、DockerFile

## 1、dockerfile介绍

> dockerfile是用来构建docker镜像的文件！命令参数脚本！

==构建步骤：==

1、编写一个dockerfile文件

2、dockerfile build 构建成为一个镜像

3、docker run 运行镜像

4、docker push 发布镜像（DockerHub、阿里云镜像仓库）

> 查看官方是怎么做的

![image-20210225124424824](https://gitee.com/coder-SJW/blogimg/raw/master/img/YRiNS41DXIpyEqv.png)

![image-20210225124440887](https://gitee.com/coder-SJW/blogimg/raw/master/img/YRiNS41DXIpyEqv.png)

**发现官方的镜像实质也是一个dockerfile**

==很多官方镜像都是基础包，很多功能没有，我们通常会搭建自己的镜像！==

## 2、dockerfile构建过程

**基础知识：**

1、每个保留关键字（指令）都必须是大写字母

2、执行顺序从上到下

3、#表示注释

4、每个指令都会创建提交一个新的镜像层，并提交！

![image-20210225125409723](https://i.loli.net/2021/02/25/jvCtOT2o9zi4YSw.png)

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，文件十分简单！

docker镜像逐渐成为企业交付的标准，必须要掌握！

## 3、dockerfile指令

```shell
FROM			#基础镜像，一切从这里开始构建MAINTAINER		#镜像是谁写的，姓名+邮箱RUN				#镜像构建时需要运行的命令ADD				#添加内容WORKDIR			#镜像的工作目录VOLUME			#挂载的目录EXPOSE			#保留端口配置CMD				#指定容器启动时运行的命令，只有最后一个会生效，可被代替ENTRYPOINT		#指定容器启动时运行的命令，可追加命令ONBUILD			#当构建一个被继承Dockerfile，这个时候就会运行ONBUILD指令。触发指令COPY			#类似ADD，将我们文件拷贝到镜像中ENY				#构建时设置环境变量，例如mysql密码
```

![image-20210225133308729](https://i.loli.net/2021/02/25/Exuyz3dJqCieZTM.png)

## 4、实战测试

> 官方的centos是简化版，很多东西不全，不如vim命令

![image-20210225163829635](https://i.loli.net/2021/02/25/Bdx4lpEUajOIRHX.png)

> 通过dockerfile创建自己的镜像，在官方原有镜像的基础上添加vim

```shell
# 1、编写dockerfile文件[root@SJW-aliyun dockerfile]# vim mydockerfile-centos[root@SJW-aliyun dockerfile]# cat mydockerfile-centos FROM centosMAINTAINER sjw<2855289411>EVN MYPATH /usr/localWORKDIR $MYPATHRUN yum -y install vimEXPOSE 80CMD echo $MYPATHCMD "----end----"CMD /bin/bash# 2、通过文件构建镜像# 命令：docker build -f dockerfile文件路径 -t 镜像名[:TAG] .# 3、测试运行
```

![image-20210225165707240](https://i.loli.net/2021/02/25/ADvWkhHRmMbVdXo.png)

![image-20210225165908727](https://i.loli.net/2021/02/25/dwAIgJ8pTshD2ay.png)

==测试成功！！！==

> 我们可以列出本地镜像的变更历史（docker history 镜像id）

![image-20210225170227228](https://i.loli.net/2021/02/25/pcO4FJnrQhmxkqV.png)

## 5、实战：Tomcat镜像

# 八、发布镜像

## 1、发布到DockerHub

==步骤：==

1、在dockerhub官网注册自己的帐号！

2、确定账户可以登陆

3、在自己的阿里云上登陆docker

> 命令：docker login

![image-20210226104132151](https://gitee.com/coder-SJW/blogimg/raw/master/img/6F54XuHbN8aUG1D.png)

4、登陆成功后就可以发布镜像

> 命令：docker push
