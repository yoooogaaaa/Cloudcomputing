#  云计算开发基础

---

## 一、实验内容：

- ### 购买腾讯云服务器并登录

  - #### 购买腾讯云服务器

  - #### 使用Web Shell登录已购买的云服务器实例

  - #### 下载安装Xshell（包含在Xmanager中），并使用Xshell登录腾讯云实例

- ### 创建GitHub项目并在本地同步

  - #### 注册Github账号

  - #### 在GitHub上创建云计算项目（Cloudcomputing）并在本地同步

- ### 本地安装VMware Workstation和CentOS操作系统

  - #### 自行安装VMware WorkStation 

    - ##### 任何版本都行，视自己机器的配置而定，比如VMware WorkStation9，12，或者15都可，版本越高要求的机器配置越高。

  - #### 在VMware WorkStation安装CentOS操作系统

  ---

  ## 二、实验过程

  ### (一)购买腾讯云服务器并登入  [购买链接](https://cloud.tencent.com/act/campus?fromSource=gwzcw.2432687.2432687.2432687&amp;utm_medium=cpc&amp;utm_id=gwzcw.2432687.2432687.2432687)

  

  #### 	1.购买CentOS 7.6 64位版本，得到一台1核 2GB 1Mbps的服务器

  ![](https://raw.githubusercontent.com/yoooogaaaa/Cloudcomputing/master/groundwork/picture%201/2.png)

  #### 	2.使用web shell登入

  #### 	3.使用Xshell登入

  ​		  由于web shell登入，每次都要用微信扫码，所以我们采用Xshell登入

  ​            1.下载xshell

  ​            2.右上角→文件→新建→输入“名称”（没有要求）→输入“主机“（公网IP）→点击链接

  ​            3.之后就可以输入密码登入

  ![](https://raw.githubusercontent.com/yoooogaaaa/Cloudcomputing/master/groundwork/picture%201/3.png)

  ### (二)创建GitHub项目并在本地同步

  #### 1.注册Github账号 [(GitHub官网)](https://github.com)

  #### 2.在GitHub上创建云计算项目（Cloudcomputing）并在本地同步

  ​	1.[安装git](https://git-scm.com/downloads)

  

  ​	2.在github创建Cloudcomputing

  ![](https://raw.githubusercontent.com/yoooogaaaa/Cloudcomputing/master/groundwork/picture%201/1.png)

  ​	3.创建本地代码仓库

  ​	4.同步

  ![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/groundwork/picture%201/7.png)

  

  ​		a.使用cd 跳转到本地仓库地址并且初始化

  ​		*git init*

  ​		b.拷贝GitHub网站中的项目网址

  ​		c.添加远程代码仓库的URL

  ​		d.首先从远程仓库拉数据

  ​		*git pull origin master*

  ​		e.新建README文档，README文档是每个GitHub项目必备，说明项目内容。上文没有创建，在此处完成。

  ​		*touch README.md*

  ​		

  ​		f.添加文件夹中的所有文件：

  ​		git add .*
  
  ​		g.查看文件是否处于准备提交的状态
  
  ​		*git status*
  
  ​        h.提交文件
  
  ​		*git commit -m “提交（这里输入什么都可以）”*
  
  ​		注意commit只在本地提交，并未同步到远程服务器。
  
  ​        i.更新
  
  ​		*git push -u origin master*
  
  ### （三）本地安装VMware Workstation和CentOS操作系统
  
  ##### 	1.安装vmware 12
  
  ##### 	2.在VMware WorkStation安装CentOS操作系统
  
  ​		a.下载centos的镜像服务器
  
  ![](https://raw.githubusercontent.com/yoooogaaaa/Cloudcomputing/master/groundwork/picture%201/6.png)
  
  ​		b.打开vmware 12，点击右上角→文件→”自定义（高级）“下一步→ ”workstation 12.0“下一步→”安装程序光盘映像文件“选择刚刚下载的镜像服务器 下一步→给虚拟机取名，并且选择安装地址→”默认设置“下一步下一步→分配2G的内存给虚拟机下一步→”桥接“下一步→”默认“下一步→”默认“下一步→ 创建新的磁盘→分配20G内存→下一步→查看自己的信息，确认无误→完成

![](https://raw.githubusercontent.com/yoooogaaaa/Cloudcomputing/master/groundwork/picture%201/4.png)





