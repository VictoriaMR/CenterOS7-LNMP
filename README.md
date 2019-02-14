# Build-UP-CenterOS-7-LNMP

### 选择虚拟机安装 VMware Workstation 14 + CenterOS7 64 mini
这里安装的是 纯命令行的CenterOS7 短小精悍 
<p align='left' style="margin-left:30px;">
<img src='images/20190213113659.png?raw=true' title='images' style='max-width:600px;'>
</p>
这里使用 WinSCP 来链接 linux 
在宿主端下载好文件直接传过去 不用命令行wget 也可以使用winSCP 远程编辑保存
<p align='left' style="margin-left:30px;">
<img src='images/2024.png?raw=true' title='images' style='max-width:600px'>
<img src='images/2025.png?raw=true' title='images' style='max-width:600px'>
</p>

步骤解读: <br>
错误标注: 开机错误,原因是服务未开 
<p align='left' style="margin-left:30px;">
<img src='images/2019.png?raw=true' title='images' style='max-width:600px'></img>
</p>
看这个界面是不是贼简单 没办法 只有一丁点的内存就节省着用吧
<p align='left' style="margin-left:30px;">
<img src='images/2020.png?raw=true' title='images' style='max-width:600px'>
</p>

## 一、初始化环境(这里建一个快照吧)
### 1.1 安装或更新gcc gcc-c++
因为我安装的Centos是绝对纯洁滴。啥也没有。没辙了。只有先安装个编译器了。

yum install gcc gcc-c++ 

### 1.2 创建需要使用的目录
source 是用来存放源码的文件夹。package是用来存放编译后的库文件。lnmp是我们真正需要的东西才放里面。（nginx+mysql+php）<br>
mkdir /source <br>
mkdir /package  <br>
mkdir /lnmp <br>
# 二、开始安装（nginx篇)

### 2.1 解压pcre

[官方网站] http://www.pcre.org/ <br>

命令流程： <br>
cd /source <br>
wget http://jaist.dl.sourceforge.net/project/pcre/pcre/8.38/pcre-8.38.tar.gz <br>
tar -zxvf pcre-8.38.tar.gz <br>
说明：不需要编译，只需要解压就行。这个是 nginx 的依赖扩展

### 2.2 解压zlib

[官方网站] http://zlib.net/ <br>
命令流程： <br>
cd /source <br> 
wget http://zlib.net/zlib-1.2.8.tar.gz  <br> 
tar -zxvf zlib-1.2.8.tar.gz  <br>
说明：不需要编译，只需要解压就行。这个是 解压 的依赖扩展 
### 2.3 安装nginx

[官方网站] http://nginx.org/ <br> 
命令流程：<br> 
cd /source <br>
wget http://nginx.org/download/nginx-1.8.0.tar.gz <br>
tar -zxvf nginx-1.8.0.tar.gz  <br>
cd nginx-1.8.0  <br>
./configure --prefix=/lnmp/nginx --with-pcre=/source/pcre-8.38 --with-zlib=/source/zlib-1.2.8 <br>
make <br>
make install  <br>
	--with-pcre：用来设置pcre的源码目录。<br>
	--with-zlib：用来设置zlib的源码目录。 <br>
 因为编译nginx需要用到这两个库的源码。

### 小章总结：

此处告一段落，nginx安装完成。我们可以先满足下自己的欲望心。打开nginx服务看看Hello World吧。
这里是纯命令行版的 怎么查看是否成功启动了nginx呢 <br> 
启动nginx: <br> 
/lnmp/nginx/sbin/nginx  <br> 
查看进程: <br> 
ps -ef | grep nginx <br> 
查看端口号: <br> 
netstat -anp | grep 80  <br> 
安装telnet: <br> 
yum install net-tools  <br> 
启动成功了!! 
<p align='left' style="margin-left:30px;">
<img src='images/2021.png?raw=true' title='images' style='max-width:600px'></img>
</p>

启动后可以再浏览器中打开页面，会显示nginx默认页面。<br>
这里因为是纯净版linux,只能通过宿主端浏览器查看是否启动成功<br>
获取ip <br>
ip addr show  
<p align='left' style="margin-left:30px;">
<img src='images/2022.png?raw=true' title='images' style='max-width:600px'></img>
</p>
允许80端口通过防火墙 <br>
/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT <br>
或者 <br>
firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效） <br>
firewall-cmd --reload 重启生效配置 <br>
打开浏览器 
<p align='left' style="margin-left:30px;">
<img src='images/2023.png?raw=true' title='images' style='max-width:600px'></img>
</p>

# 三、开始安装（php篇）

### 3.1 安装libxml2
[官方网站] http://xmlsoft.org/ <br>
命令流程： <br>
cd /source  <br>
wget ftp://xmlsoft.org/libxml2/libxml2-2.9.9.tar.gz  <br>
tar -zxvf libxml2-2.9.9.tar.gz  <br>
cd libxml2-2.9.9 <br>
./configure  <br>
--prefix=/package/libxml2 --with-python=no  <br>
make  <br>
make install  <br>
这里--with-python=no是 不使用python  <br>

### 3.2 安装php
[官方网站] http://php.net/ 
命令流程： 

cd /source  
wget http://cn2.php.net/distributions/php-7.0.2.tar.gz
tar -zxvf php-7.0.2.tar.gz  
cd php-7.0.2  

./configure \ 
--prefix=/lnmp/php \ 
--with-libxml-dir=/package/libxml2 \ //打开libxml2库的支持  
--with-config-file-path=/lnmp/php/etc \ //配置文件所在目录  
--enable-mbstring \      //支持mbstring库  
--enable-fpm \       //支持php-fpm（推荐打开）  
--with-mysqli       //打开mysqli模块  

make  
make install  

到这里 php 已经装好了  

# 四、开始安装（mysql篇）

### 4.1 安装 mysql

看网站的教程 千篇不一样 还是老老实实看官方的安装文档 
这里不选择rpm 安装 选择二进制安装文件 
因为感觉后者更自定义多一些吧 

### 4.1.1 下载源码包
wget https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-boost-8.0.15.tar.gz  

### 4.1.2 解压源码包
tar -xJvf mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz -C /usr/local/  
mv /usr/local/mysql-8.0.15-linux-glibc2.12-x86_64/ /usr/local/mysql  

### 4.1.3 配置参数文件：
cat /etc/my.cnf  
[mysqld]  
server-id                      = 1  
port                           = 3306  
mysqlx_port                    = 33060  
mysqlx_socket                  = /tmp/mysqlx.sock  
datadir                        = /data/mysql  
socket                         = /tmp/mysql.sock  
pid-file                       = /tmp/mysqld.pid  
log-error                      = error.log  
slow-query-log                 = 1  
slow-query-log-file            = slow.log  
long_query_time                = 0.2  
log-bin                        = bin.log  
relay-log                      = relay.log  
binlog_format                  =ROW  
relay_log_recovery             = 1  
character-set-client-handshake = FALSE  
character-set-server           = utf8mb4  
collation-server               = utf8mb4_unicode_ci  
init_connect                   ='SET NAMES utf8mb4'  
innodb_buffer_pool_size        = 1G  
join_buffer_size               = 128M  
sort_buffer_size               = 2M  
read_rnd_buffer_size           = 2M  
log_timestamps                 = SYSTEM  
lower_case_table_names         = 1  
default-authentication-plugin  =mysql_native_password  
 
###4.1.4 创建目录授权等：
groupadd mysql  
useradd mysql  
mkdir -p /data/mysql  
chown -R mysql:mysql /data/mysql/  
chmod -R 775 /data/mysql/  
 
 
### 4.1.5 初始化数据库：
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql --initialize-insecure  
官方推荐使用--initialize，会在错误日志中生成难以输入的临时密码，我这里使用的免密码的方式。 

### 4.1.6 设置启动文件和环境变量：
cp /usr/local/mysql/support-files/mysql.server  /etc/init.d/mysql  
启动数据库： 
/etc/init.d/mysql start  
 Starting MySQL.Logging to '/data/mysql/error.log'. 

加入环境变量: 
export PATH=$PATH:/usr/local/mysql/bin 

测试一下: 
mysql --version  
mysql  
<p align='left' style="margin-left:30px;">
<img src='images/2030.png?raw=true' title='images' style='max-width:600px'></img>

到此 mysql 数据库已经安装好了 这里建一个快照 

想要宿主可以链接本数据库  还需要开放一下3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent 
firewall-cmd --reload 
如果不行的话应该是mysql 的权限没有刷新 
mysql 
flush privileges; 

--------------------- 
部分参考
原文：https://blog.csdn.net/vkingnew/article/details/81267223   

# 五、 配置Nginx+PHP
### 5.1 修改nginx.conf
<p align='left' style="margin-left:30px;">
<img src='images/2031.png?raw=true' title='images' style='max-width:600px'></img>
如图所示 , 把注释去掉 , 意思是脚本交给 php 去处理 

### 5.2 配置（php-fpm）
cd /source/php-7.3.2 
cp php.ini-development /lnmp/php/etc/php.ini  
cd /lnmp/php/etc   
cp php-fpm.conf.default php-fpm.conf  
cd /lnmp/php/etc/php-fpm.d/  
cp www.conf.default www.conf  

### 5.2.1运行php-fpm

/lnmp/php/sbin/php-fpm -c /lnmp/php/etc/php.ini  

注:打开浏览器php文件出现file not found 的情况,而html正常 则是php解析路径没有配好
<p align='left' style="margin-left:30px;">
<img src='images/2033.png?raw=true' title='images' style='max-width:600px'></img>
</p>

到这里 lnmp 环境算是初步搭建完成了
<p align='left' style="margin-left:30px;">
<img src='images/2034.png?raw=true' title='images' style='max-width:600px'></img>
</p>







