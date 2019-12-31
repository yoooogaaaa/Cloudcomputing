# Ceph的安装与实践

### 在虚拟机上安装centos镜像

[镜像下载链接](https://mirrors.tuna.tsinghua.edu.cn/centos/7.7.1908/isos/x86_64/)

## 步骤1-配置所有节点

### 创建Cephuser用户

```shell
useradd -d /home/cephuser -m cephuser
passwd cephuser
```

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(15).png?raw=true)

授予权限（创建一个sudoers文件，并使用sed编辑/etc/sudoers文件）

```shell
echo "cephuser ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/cephuserchmod 0440 /etc/sudoers.d/cephusersed -i s'/Defaults requiretty/#Defaults requiretty'/g /etc/sudoers
```

![ (1)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(1).png?raw=true)

安装和配置NTP

```shell
yum install -y ntp ntpdate ntp-doc
ntpdate 0.us.pool.ntp.org
hwclock --systohc
systemctl enable ntpd.service
systemctl start ntpd.service
```

![ (3)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(3).png?raw=true)

禁用SELinux

```shell
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### 配置主机文件

```shell
vim /etc/hosts
```

输入（根据实际的IP配置，可通过ip add查看ip）：

10.0.15.10       admin
10.0.15.11        mon
10.0.15.21        osd1
10.0.15.22        osd2

保存文件并退出vim。

试着看看能不能ping通

## 步骤2-配置SSH服务器

配置admin结点

```shell
ssh root@admin
su - cephuser
```

生成ssh

```shell
ssh-keygen
```

创建配置文件

```shell
vim ~/.ssh/config
```

黏贴如下文件：

```shell
Host ceph-admin        
Hostname ceph-admin        
User cephuser 
Host mon1
Hostname mon1
User cephuser 
Host osd1
Hostname osd1
User cephuser
Host osd2        
Hostname osd2       
User cephuser
```

保存文件

更改配置文件的权限。

```shell
chmod 644 ~/.ssh/config
```

使用ssh-Copy-id命令将SSH密钥添加到所有节点

```shell
ssh-keyscan osd1 osd2 mon1 >> ~/.ssh/known_hosts
ssh-copy-id osd1
ssh-copy-id osd2
ssh-copy-id mon1
```

![ (4)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(4).png?raw=true)

![ (5)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(5).png?raw=true)完成后，尝试从Ceph-admin节点访问osd 1服务器

```shell
ssh osd1
```

![ (6)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(6).png?raw=true)

## 步骤3-配置Firewalld

登录到admin节点并启动Firewalld。

```shell
ssh root@admin
systemctl start firewalld
systemctl enable firewalld
```

打开端口80，2003和4505-4506，然后重新加载防火墙.

```shell
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
sudo firewall-cmd --zone=public --add-port=2003/tcp --permanent
sudo firewall-cmd --zone=public --add-port=4505-4506/tcp --permanent
sudo firewall-cmd --reload
```

![ (7)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(7).png?raw=true)

从Ceph-admin节点登录到监视器节点‘mon1’并启动Firewalld

```shell
ssh mon1
sudo systemctl start firewalld
sudo systemctl enable firewalld
```

在cephuser'监视器节点上打开新端口并重新加载防火墙。

```shell
sudo firewall-cmd --zone=public --add-port=6789/tcp --permanent
sudo firewall-cmd --reload
```

![ (8)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(8).png?raw=true)

最后，开放端口6800-7300对每个osd节点-osd1，osd2

```shell
ssh osd1
sudo systemctl start firewalld
sudo systemctl enable firewalld
```

打开端口并重新加载防火墙。

```shell
sudo firewall-cmd --zone=public --add-port=6800-7300/tcp --permanent
sudo firewall-cmd --reload
```

![ (9)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(9).png?raw=true)

完成了Firewalld配置。

## 步骤4-配置CephOSD节点

我们会用**/dev/sdb**去找卡夫磁盘。从Ceph-admin节点登录到所有OSD节点，并将/dev/sdb分区格式化为**XFS**.

```
ssh osd1
ssh osd2
```

使用fdisk命令检查分区。

```shell
sudo fdisk -l /dev/sdb
```

使用Parted命令用XFS文件系统和GPT分区表格式化/dev/sdb分区。

```shell
sudo parted -s /dev/sdb mklabel gpt mkpart primary xfs 0% 100%
sudo mkfs.xfs /dev/sdb -f
```

现在检查分区，将得到XFS/dev/sdb分区。

```shell
sudo blkid -o value -s TYPE /dev/sdb
```

![ (10)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(10).png?raw=true)

## 步骤5-构建cephuser集群

登录到Ceph-admin节点。

```shell
ssh root@ceph-admin
su - cephuser
```

在Ceph-admin节点上安装cephuser-Deploy

```shell
sudo rpm -Uhv http://download.ceph.com/rpm-jewel/el7/noarch/ceph-release-1-1.el7.noarch.rpm
sudo yum update -y && sudo yum install ceph-deploy -y
```

![ (11)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(11).png?raw=true)

安装了Ceph-Deploy工具之后，为Cephuser集群配置创建一个新目录

创建目录

```shell
mkdir cluster
cd cluster/
```

使用**Ceph-Deploy**命令，将监视器节点定义为‘mon'

```shell
ceph-deploy new mon
```

该命令将在集群目录中生成Cephuser集群配置文件“ceph.conf”。

用vim编辑ceph.conf文件。

```shell
vim ceph.conf
```

粘贴下面的配置。

```shell
# Your network address
public network = 10.0.15.0/24
osd pool default size = 2
```

![ (13)](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(13).png?raw=true)

保存文件并退出vim。

### 在所有节点上安装

使用下面的命令

```shell
ceph-deploy install ceph-admin mon1 osd1 osd2 
```

该命令将自动在所有节点上安装cephuser：mon、osd 1-2和ceph-admin-安装将花费一些时间

在mon1节点上部署Ceph-mon

```shell
ceph-deploy mon create-initial
```

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(16).png?raw=true)

该命令将创建监视器键，使用“cephuser”命令检查并获取键。

```shell
ceph-deploy gatherkeys mon1
```

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(17).png?raw=true)

### 在集群中添加OSDS

使用以下命令激活OSD

```
ceph-deploy osd activate osd1:/dev/sdb1 osd2:/dev/sdb1
```

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(20).png?raw=true)

在继续之前，请检查输出中是否有错误。现在，您可以使用List命令检查OSD节点上的SDB磁盘。

```
ceph-deploy disk list osd1 osd2
```

接下来，将管理密钥部署到所有相关节点。

```shell
ceph-deploy admin ceph-admin mon1 osd1 osd2 
```

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(21).png?raw=true)



通过在所有节点上运行下面的命令来更改密钥文件的权限。

```
sudo chmod 644 /etc/ceph/ceph.client.admin.keyring
```

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(22).png?raw=true)

CentOS 7上的cephuser集群已经创建

## 步骤6-测试cephuser设置

从Ceph-admin节点登录到cephuser监视器服务器‘**mon**'.

```
ssh mon1
```



进入ceph

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(23).png?raw=true)



运行下面的命令以检查群集运行情况。

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/(25).png?raw=true)

![](https://github.com/yoooogaaaa/Cloudcomputing/blob/master/CEPH/PIC/%20(24).png?raw=true)

恭喜，你成功地建立了一个新的Cephuser集群。

