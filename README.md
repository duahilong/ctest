# Linux  
## Centos7替换Yum源  
- 备份:  
        
        mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
- 下载:  
**1. 阿里云**

        wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
- 更新Cache:  
        
        yum clean all && yum makecache
------
## 安装Epel -替换epel源  
- 安装epel  

        yum -y install epel-release
- 替换源  

  自动替换清华epel源  
    ```
    sed -e 's!^metalink=!#metalink=!g' \
    -e 's!^#baseurl=!baseurl=!g' \
    -e 's!//download\.fedoraproject\.org/pub!//mirrors.tuna.tsinghua.edu.cn!g' \
    -e 's!http://mirrors\.tuna!https://mirrors.tuna!g' \
    -i /etc/yum.repos.d/epel.repo /etc/yum.repos.d/epel-testing.repo
    ```
    帮助：<https://mirrors.tuna.tsinghua.edu.cn/help/epel/>
------
## Centos7安装Python3  
- 查看Python安装位置  
        
        whereis python
- 安装编译Python3相关软件  
        
        yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel -y
- 安装Pip  
        
        yum -y install epel-release && yum install python-pip
- 创建Python3下载目录  
        
        mkdir python3 && cd python3
- 下载Python3  
        
        wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
- 解压缩  
        
        tar -zxvf Python-3.7.0.tgz
- 进入解压后的目录，依次执行下面命令进行手动编译  
        
        ./configure prefix=/usr/local/python3
        make && make install
- 添加软连接  
        
        ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3
        ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3
-  查看版本信息  
        
        python3 -V
- 修改Yum源配置  
        
        vim /usr/bin/yum
         /usr/bin/python 修改为 /usr/bin/python2
        vim /usr/libexec/urlgrabber-ext-down
         /usr/bin/python 修改为 /usr/bin/python2  
----------
## Centos7替换Pip源  
- 临时使用  
        
        pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
- 设置默认    
**1. 清华大学**  
        
        pip3 install pip -U
        pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
--------
## 阿里云DDNS For Linux  
- 安装pip3依赖包  
        
        pip3 install aliyun-python-sdk-core-v3 aliyun-python-sdk-alidns requests
- 源码克隆到本地  
        
        git clone https://github.com/terencechuen/aliyun_ddns
- 备份配置文件  
        
        cp aliyun_ddns/config_template.json aliyun_ddns/config.conf
- 配置定时任务  
        
        vim /etc/crontab
        */2 * * * * root /usr/bin/python3 /root/aliyun_ddns/aliyun_ddns.py > /dev/null 2>&1
- 重启crontab  

        systemctl restart crond.service
---------
## 安装SoftEtherVPN Server  
- 安装编译环境  

        yum install gcc -y
- 下载软件 Softethervpn For Linux  

        wget https://github.com/SoftEtherVPN/SoftEtherVPN_Stable/releases/download/v4.34-9744-beta/softether-vpnserver-v4.34-9744-beta-2020.03.20-linux-x64-64bit.tar.gz
- 解压文件  

        tar -zxvf softether-vpnserver-*.tar.gz
- 进入解压目录  
        
        cd vpnserver
- 安装  

        ./install.sh
- 启动服务  
        
        ./vpnserver start
- 停止服务  

        ./vpnserver stop
- 创建启动脚本  
        
        vim /etc/systemd/system/vpnserver.service  
          

        [Unit]
        Description=SoftEther VPN Server
        After=network.target

        [Service]
        Type=forking
        ExecStart=/root/vpnserver/vpnserver start
        ExecStop=/root/vpnserver/vpnserver stop

        [Install]
        WantedBy=multi-user.target
----
## 关闭SELinux  
        vim /etc/selinux/config
- 将SELINUX=enforcing改为SELINUX=disabled
设置后需要重启才能生效

---------
## 安装Netdata-监控软件  
1. 自动安装  
        
        bash <(curl -Ss https://my-netdata.io/kickstart.sh)  
2. 编译安装  
    - 安装依赖软件  

        **基本安装**
        ```
        curl -Ss 'https://raw.githubusercontent.com/firehol/netdata-demo-site/master/install-required-packages.sh' >/tmp/kickstart.sh && bash /tmp/kickstart.sh -i netdata
        ```
        **完整安装**
        ```
        curl -Ss 'https://raw.githubusercontent.com/firehol/netdata-demo-site/master/install-required-packages.sh' >/tmp/kickstart.sh && bash /tmp/kickstart.sh -i netdata-all
        ```
    - 安装Netdata  
        ```
        ##下载
        git clone https://github.com/firehol/netdata.git --depth=1
        cd netdata

 
        ##安装
        ./netdata-installer.sh


        ##汉化安装
        ./netdata-installer-zh.sh
        ```
    - 运行Netdata  
        ```
        service netdata start
        ```
    <font size=4>So you can access it with:http://this.machine.ip:19999</font>  

    <font size=4>中文汉化：<https://github.com/YuanzhuL/netdata-cpatch></font>  
    <font size=4>项目地址：<https://github.com/netdata/netdata></font>  
    <font size=4>中文项目地址：<https://github.com/Fhaohaizi/netdata></font>
    




## Zabbix二进制包安装

- Centos 7 添加阿里云镜像  

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum install epel-release
```


- 安装常用的开发组件  
```
yum  groups install "Development Tools"
yum groups info  "Development Tools"
```

- 增加Zabbix镜像源  
```
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
rpm -ivh https://mirror.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
```
- 替换Zabbix镜像源 
```
echo  -e "[zabbix]\nname=Zabbix Repository\nbaseurl=https://mirror.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/7/x86_64/\ngpgcheck=0\nenabled=1" >>/etc/yum.repos.d/zabbix.repo
```

- 关闭SElinux  
```
systemctl stop firewalld.service 
systemctl disable  firewalld.service
vim /etc/selinux/config
yum install iptables-services
```

- 安装Zabbix Server和Frontend  
```
yum install zabbix-server-mysql
yum install zabbix-web-mysql
```

- 安装MySQL  
```
yum install mariadb-server
systemctl  start mariadb.service
systemctl  status mariadb.service
mysql_secure_installation
```

- 创建数据库  
```
# mysql -uroot -p
password
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> quit;
```
- 导入数据结构  
```
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
```

- 配置Zabbix Serve  
```
vim /etc/zabbix/zabbix_server.conf 
DBPassword=password
```

- 启动Zabbix Server  
```
systemctl  start zabbix-server.service
systemctl  status zabbix-server.service
more /var/log/zabbix/zabbix_server.log
```

- 配置Zabbix frontend  
```
vim /etc/php.ini

max_execution_time = 300
memory_limit = 128M
post_max_size = 16M
upload_max_filesize = 2M
max_input_time = 300
max_input_vars = 10000
always_populate_raw_post_data = -1
date.timezone = Asia/Shanghai
```

- 启动httpd  
```
systemctl  start  httpd.service
systemctl  status  httpd.service

http://10.211.55.7/zabbix/setup.php

Admin
zabbix
```

- 安装Zabbix Agent  
```
yum install zabbix-agent
systemctl  start zabbix-agent.service
```
- 替换Zabbix字体 
```
/usr/share/zabbix/assets/fonts 
cp 你上传的字体名称 graphfont.ttf
```

## Zabbix源码包安装

- 下载zabbix server源码包
```
wget -O zabbix-4.0.4.tar.gz  https://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/4.0.4/zabbix-4.0.4.tar.gz/download
```
- 安装依赖
```
yum install wget telnet net-tools python-paramiko gcc gcc-c++ dejavu-sans-fonts python-setuptools python-devel sendmail mailx net-snmp net-snmp-devel net-snmp-utils freetype-devel libpng-devel perl unbound libtasn1-devel p11-kit-devel OpenIPMI unixODBC  libevent-devel  mysql-devel  libxml2-devel  libssh2-devel  OpenIPMI-devel  java-1.8.0-openjdk-devel  openldap-devel  curl-devel unixODBC-devel
```
- 解压并编译安装
```
tar -zxvf zabbix-4.0.4.tar.gz
cd  zabbix-4.0.4/
```
- 建立编译安装目录
```
mkdir -p /data/findsec/zabbix
./configure --prefix=/data/findsec/zabbix  --enable-server  --enable-agent --enable-java  --with-mysql --with-libxml2 --with-unixodbc  --with-net-snmp --with-ssh2 --with-openipmi --with-ldap --with-libcurl --with-iconv
make
make install


/data/findsec/zabbix/sbin/zabbix_server  -V
```

- 数据库安装
```
yum install mariadb-server
systemctl  start mariadb.service
systemctl  status mariadb.service
mysql_secure_installation
```
- 创建数据库
```
mysql -uroot -p
create database zabbix character set utf8 collate utf8_bin;
grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix@findsec123';
```
- 导入数据结构
```
mysql -uzabbix -pzabbix@findsec123  zabbix < /root/zabbix-4.0.4/database/mysql/schema.sql 
mysql -uzabbix -pzabbix@findsec123  zabbix < /root/zabbix-4.0.4/database/mysql/images.sql
mysql -uzabbix -pzabbix@findsec123  zabbix < /root/zabbix-4.0.4/database/mysql/data.sql 
```
- 修改zabbix server配置
```
vim /data/findsec/zabbix/etc/zabbix_server.conf
```
- 增加用户
```
groupadd --system zabbix
useradd --system -g zabbix -d /usr/lib/zabbix -s /sbin/nologin -c "Zabbix Monitoring System" zabbix
```
- 启动服务
```
/data/findsec/zabbix/sbin/zabbix_server -c /data/findsec/zabbix/etc/zabbix_server.conf

tailf /tmp/zabbix_server.log
```

- 增加Zabbix镜像源
```
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
```
- 安装Zabbix Frontend
```
yum install zabbix-web-mysql
```
- 配置Zabbix frontend
```
vim /etc/php.ini

max_execution_time = 300
memory_limit = 128M
post_max_size = 16M
upload_max_filesize = 2M
max_input_time = 300
max_input_vars = 10000
always_populate_raw_post_data = -1
date.timezone = Asia/Shanghai
```

- 启动httpd
```
systemctl  start  httpd.service
systemctl  status  httpd.service

http://10.211.55.7/zabbix/setup.php

Admin
zabbix

```
- 安装Zabbix Agent
```
yum install zabbix-agent

systemctl  start zabbix-agent.service
```
