# Build-UP-CenterOS-7-LNMP

### 选择虚拟机安装 VMware Workstation 14 + CenterOS7 64 mini
这里安装的是 纯命令行的CenterOS7 短小精悍 <br />
<p align='left' style="margin-left:30px";>
<img src='images/20190213113659.png?raw=true' title='images' style='max-width:600px'></img>
</p>
这里使用 WinSCP 来链接 linux <br />
在宿主端下载好文件直接传过去 不用命令行wget 也可以使用winSCP 远程编辑保存<br />
<p align='left' style="margin-left:30px";>
<img src='images/2024.png?raw=true' title='images' style='max-width:600px'></img>
<img src='images/2025.png?raw=true' title='images' style='max-width:600px'></img>
</p>

### 步骤解读：
错误标注: <br />
开机错误,原因是服务未开 <br />
<p align='left' style="margin-left:30px";>
<img src='images/2019.png?raw=true' title='images' style='max-width:600px'></img>
</p>

看这个界面是不是贼简单 没办法 只有一丁点的内存就节省着用吧 <br />
<p align='left' style="margin-left:30px";>
<img src='images/2020.png?raw=true' title='images' style='max-width:600px'></img>
</p>
<br />
#一、初始化环境(这里建一个快照吧)
###1.1 安装或更新gcc gcc-c++
因为我安装的Centos是绝对纯洁滴。啥也没有。没辙了。只有先安装个编译器了。<br />
yum install gcc gcc-c++ <br />

###1.2 创建需要使用的目录
source 是用来存放源码的文件夹。package是用来存放编译后的库文件。lnmp是我们真正需要的东西才放里面。（nginx+mysql+php）<br />
mkdir /source <br />
mkdir /package <br />
mkdir /lnmp <br />
#二、开始安装（nginx篇）

###2.1 解压pcre

[官方网站] http://www.pcre.org/ <br />

命令流程： <br />
cd /source <br />
wget http://jaist.dl.sourceforge.net/project/pcre/pcre/8.38/pcre-8.38.tar.gz<br />
tar -zxvf pcre-8.38.tar.gz<br />
说明：不需要编译，只需要解压就行。这个是 nginx 的依赖扩展

###2.2 解压zlib <br />

[官方网站] http://zlib.net/ <br />

命令流程： <br />
cd /source  <br />
wget http://zlib.net/zlib-1.2.8.tar.gz  <br />
tar -zxvf zlib-1.2.8.tar.gz  <br />
说明：不需要编译，只需要解压就行。这个是 解压 的依赖扩展 <br />

###2.3 安装nginx

[官方网站] http://nginx.org/ <br />

命令流程：<br />

cd /source <br />
wget http://nginx.org/download/nginx-1.8.0.tar.gz
tar -zxvf nginx-1.8.0.tar.gz <br />
cd nginx-1.8.0 <br />
./configure --prefix=/lnmp/nginx --with-pcre=/source/pcre-8.38 --with-zlib=/source/zlib-1.2.8 <br />
make <br />
make install <br />
	--with-pcre：用来设置pcre的源码目录。<br />
	--with-zlib：用来设置zlib的源码目录。<br />
 因为编译nginx需要用到这两个库的源码。<br />

###小章总结：<br />

此处告一段落，nginx安装完成。我们可以先满足下自己的欲望心。打开nginx服务看看Hello World吧。<br />
这里是纯命令行版的 怎么查看是否成功启动了nginx呢 <br />
启动nginx: <br />
/lnmp/nginx/sbin/nginx <br />
查看进程:<br />
ps -ef | grep nginx <br />
查看端口号:<br />
netstat -anp | grep 80 <br />
安装telnet:<br />
yum install net-tools <br />

启动成功了!!<br />
<p align='left' style="margin-left:30px";>
<img src='images/2021.png?raw=true' title='images' style='max-width:600px'></img>
</p>

启动后可以再浏览器中打开页面，会显示nginx默认页面。
这里因为是纯净版linux,只能通过宿主端浏览器查看是否启动成功
获取ip<br />
ip addr show <br />
<p align='left' style="margin-left:30px";>
<img src='images/2022.png?raw=true' title='images' style='max-width:600px'></img>
</p>
允许80端口通过防火墙<br />
/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT<br />
或者<br />
firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）<br />
firewall-cmd --reload 重启生效配置<br />
打开浏览器<br />
<p align='left' style="margin-left:30px";>
<img src='images/2023.png?raw=true' title='images' style='max-width:600px'></img>
</p>

#三、开始安装（php篇）

###3.1 安装libxml2
[官方网站] http://xmlsoft.org/<br />
命令流程：<br />

cd /source <br />
wget ftp://xmlsoft.org/libxml2/libxml2-2.9.9.tar.gz<br />
tar -zxvf libxml2-2.9.9.tar.gz<br />
cd libxml2-2.9.9<br />
./configure <br />
--prefix=/package/libxml2 --with-python=no <br />
make <br />
make install <br />
这里--with-python=no是 不使用python

###3.2 安装php
[官方网站] http://php.net/<br />
命令流程：<br />

cd /source <br />
wget http://cn2.php.net/distributions/php-7.0.2.tar.gz
tar -zxvf php-7.0.2.tar.gz <br />
cd php-7.0.2 <br />

./configure \<br />
--prefix=/lnmp/php \<br />
--with-libxml-dir=/package/libxml2 \ //打开libxml2库的支持 <br />
--with-config-file-path=/lnmp/php/etc \ //配置文件所在目录 <br />
--enable-mbstring \      //支持mbstring库 <br />
--enable-fpm \       //支持php-fpm（推荐打开） <br />
--with-mysqli       //打开mysqli模块 <br />

make <br />
make install <br />

到这里 php 已经装好了 <br />

#四、开始安装（mysql篇）

###4.1 安装 mysql

看网站的教程 千篇不一样 还是老老实实看官方的安装文档<br />
这里不选择rpm 安装 选择二进制安装文件<br />
因为感觉后者更自定义多一些吧<br />

###4.1.1 下载源码包
wget https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-boost-8.0.15.tar.gz <br />

###4.1.2 下载源码包
tar -xJvf mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz -C /usr/local/ <br />
mv /usr/local/mysql-8.0.15-linux-glibc2.12-x86_64/ /usr/local/mysql <br />

###4.1.3 配置参数文件：
cat /etc/my.cnf <br />
[mysqld] <br />
server-id                      = 1 <br />
port                           = 3306 <br />
mysqlx_port                    = 33060 <br />
mysqlx_socket                  = /tmp/mysqlx.sock <br />
datadir                        = /data/mysql <br />
socket                         = /tmp/mysql.sock <br />
pid-file                       = /tmp/mysqld.pid <br />
log-error                      = error.log <br />
slow-query-log                 = 1 <br />
slow-query-log-file            = slow.log <br />
long_query_time                = 0.2 <br />
log-bin                        = bin.log <br />
relay-log                      = relay.log <br />
binlog_format                  =ROW <br />
relay_log_recovery             = 1 <br />
character-set-client-handshake = FALSE <br />
character-set-server           = utf8mb4 <br />
collation-server               = utf8mb4_unicode_ci <br />
init_connect                   ='SET NAMES utf8mb4' <br />
innodb_buffer_pool_size        = 1G <br />
join_buffer_size               = 128M <br />
sort_buffer_size               = 2M <br />
read_rnd_buffer_size           = 2M <br />
log_timestamps                 = SYSTEM <br />
lower_case_table_names         = 1 <br />
default-authentication-plugin  =mysql_native_password <br />
 
###4.1.4 创建目录授权等：
groupadd mysql <br />
useradd mysql <br />
mkdir -p /data/mysql <br />
chown -R mysql:mysql /data/mysql/ <br />
chmod -R 775 /data/mysql/ <br />
 
 
###4.1.5 初始化数据库：
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql --initialize-insecure <br />
官方推荐使用--initialize，会在错误日志中生成难以输入的临时密码，我这里使用的免密码的方式。<br />

###4.1.6 设置启动文件和环境变量：
cp /usr/local/mysql/support-files/mysql.server  /etc/init.d/mysql <br />
启动数据库：<br />
/etc/init.d/mysql start <br />
 Starting MySQL.Logging to '/data/mysql/error.log'.<br />

加入环境变量:<br />
export PATH=$PATH:/usr/local/mysql/bin<br />

测试一下:<br />
mysql --version <br />
mysql <br />
<p align='left' style="margin-left:30px";>
<img src='images/2030.png?raw=true' title='images' style='max-width:600px'></img>

到此 mysql 数据库已经安装好了 这里建一个快照<br />

想要宿主可以链接本数据库  还需要开放一下3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent<br />
firewall-cmd --reload<br />
如果不行的话应该是mysql 的权限没有刷新<br />
mysql<br />
flush privileges;<br />

--------------------- 
部分参考
原文：https://blog.csdn.net/vkingnew/article/details/81267223  <br />

#五、 配置Nginx+PHP
###5.1 修改nginx.conf
<p align='left' style="margin-left:30px";>
<img src='images/2031.png?raw=true' title='images' style='max-width:600px'></img>
如图所示 , 把注释去掉 , 意思是脚本交给 php 去处理<br />

###5.2 配置（php-fpm）
cd /source/php-7.3.2<br />
cp php.ini-development /lnmp/php/etc/php.ini <br />
cd /lnmp/php/etc <br /> 
cp php-fpm.conf.default php-fpm.conf <br />
cd /lnmp/php/etc/php-fpm.d/ <br />
cp www.conf.default www.conf <br />

###5.2.1运行php-fpm

/lnmp/php/sbin/php-fpm -c /lnmp/php/etc/php.ini <br />

注:打开浏览器php文件出现file not found 的情况,而html正常 则是php解析路径没有配好
<p align='left' style="margin-left:30px";>
<img src='images/2033.png?raw=true' title='images' style='max-width:600px'></img>
</p>

到这里 lnmp 环境算是初步搭建完成了

<p align='left' style="margin-left:30px";>
<img src='images/2034.png?raw=true' title='images' style='max-width:600px'></img>
</p>







