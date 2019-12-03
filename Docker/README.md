## 实验三：Docker



#### 一、安装docker

#### 二、完成Docker安装之后加载CentOS镜像

#### 三、在Docker的CentOS容器实例中安装WordPress

#### 四、将带有WordPress的CentOS镜像推送到容器仓库

#### 五、Dockerfile



##### 一、安装docker

1.使用如下命令查看操作系统内核信息：

```
uname -r
```

2.查看版本号

```
cat /etc/redhat-release
```

![](http://106.54.62.234:8888/wp-content/uploads/2019/12/test3-2.png)

3.更新数据库

```
 yum check-update
```

4.接下来添加Docker的官方仓库，下载最新的Docker并安装：

```
curl -fsSL https://get.docker.com/ | sh
```

这个是docker官网提供的一个一键脚本安装↑

5.安装完后，启动docker

```
systemctl start docker
```

6.验证Docker是否成功启动：

```
sudo systemctl status docker
```

![](http://106.54.62.234:8888/wp-content/uploads/2019/12/test3-1.png)

7.设置Docker自启动

```
sudo systemctl enable docker
```

8.查看一下Docker的版本信息：

```
docker version
```

![](http://106.54.62.234:8888/wp-content/uploads/2019/12/test3-6.png)

##### 二、完成Docker安装之后加载CentOS镜像

1.接下来拉取官方版本(OFFICIAL)的镜像：

```
docker pull centos:7
```

2.一旦镜像下载完成，可以基于该镜像运行容器，使用run命令：

```
docker run centos:7
```

3.查看一下当前系统中存在的镜像：

```
docker images
```

4.**运行Docker容器（为了方便检测后续wordpress搭建是否成功，需设置端口映射（-p），将容器端口80 映射到主机端口8888，Apache和MySQL需要 systemctl 管理服务启动，需要加上参数 --privileged 来增加权，并且不能使用默认的bash，换成 init，否则会提示 Failed to get D-Bus connection: Operation not permitted ，，-name 容器名 ，命令如下 ）**

```
docker run -d -it --privileged --name wordpress -p 8888:80 -d centos:7 /usr/sbin/init

```

5.查看已启动的容器

```
docker ps
```

6.进入容器前台（容器id可以只写前几位，如 ：de4）

```
docker exec -it de4 /bin/bash
```

![](http://106.54.62.234:8888/wp-content/uploads/2019/12/test3-4.png))

##### 三、在Docker的CentOS容器实例中安装WordPress

1.参考[基于腾讯云创建个人网站](https://github.com/yoooogaaaa/Cloudcomputing/tree/master/website)

> **为了避免冲突，所以将镜像的公网IP加上端口:8888**
>
> **安装完成后可访问 服务器IP:8888 查看**

![镜像的网站](http://106.54.62.234:8888/wp-content/uploads/2019/12/test3-3.png)

![原来的网站](http://106.54.62.234:8888/wp-content/uploads/2019/12/814Z9TAWYDZ3JVJNPW9U.png)

##### 四、将带有WordPress的CentOS镜像推送到容器仓库

1.前往[docker hub](https://hub.docker.com/)注册账号

2.使用如下命令查看本地中的容器：

```
docker ps -a
```

3.使用commit命令来提交更改到新的镜像中，即创建新的镜像。命令格式

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

例：docker commit -a "yoga" -m "wordpress on centos7"de3cfaxxxxx  yoga/centos:v1

这种提交类似于git协议的提交，同样这里提交的镜像只保存在本地。后续可以提交到远程镜像仓库，比如Docker Hub。

4.登录Docker

```
docker login
```

输入刚刚注册的账号密码

![](http://106.54.62.234:8888/wp-content/uploads/2019/12/test3-5.png)

5.推送镜像

```
docker push 镜像名:tag标签
```

###### 例: docker push yoga/centos:v1

登录Docker网页查看仓库

##### 

![](http://106.54.62.234:8888/wp-content/uploads/2019/12/test3-1.jpg)

#### 五、Dockerfile

◼ 安装Apache Web服务器

1.在本地主机新建一个目录（本文为mydocker）存放Dockerfile文件，新建Dockerfile文件：

mkdir /mydocker

cd /mydocker

vim Dockerfile

添加以下内容：

```
FROM centos:latest

LABEL project="Dockerfile for Apache Web"

RUN yum -y install httpd

EXPOSE 80

VOLUME /var/www/html

ENTRYPOINT [ "/usr/sbin/httpd" ]
CMD ["-D", "FOREGROUND"]
```

![img](http://106.54.62.234:8888/wp-content/uploads/2019/12/2-4.png)

2.生成docker镜像

docker build -t centos:httpd .

![img](http://106.54.62.234:8888/wp-content/uploads/2019/12/2-2.png)

3.启动容器实例

mkdir /data

docker run -td -p 8000:80 -v /data:/var/www/html --name=web centos:httpd

![img](http://106.54.62.234:8888/wp-content/uploads/2019/12/2-3-1024x47.png)

这里-p指定本地主机和容器的端口映射，-v指定数据挂载（volume）。

查看启动的容器实例：

4.验证Apache Web（Httpd）是否安装成功

cd /data

vim index.html

随意添加一些内容：

最后使用"http://localhost/"进行测试，得到如下结果：

![img](http://106.54.62.234:8888/wp-content/uploads/2019/12/2-1.png)



◼ 安装MySQL



◼ 安装PHP



◼ 安装WordPress

