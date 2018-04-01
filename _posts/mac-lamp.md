---
title: Mac MAMP
date: 2018-03-31 14:17:24
tags:
	- mac
	- apache
	- mysql
	- php
categories: mamp
description: Create apache, mysql, php environment on Mac.
---
> [apache+php](https://getgrav.org/blog/macos-sierra-apache-multiple-php-versions)
> [mysql+vhost+apc](https://getgrav.org/blog/macos-sierra-apache-mysql-vhost-apc)
> [apache ssl](https://getgrav.org/blog/macos-sierra-apache-ssl)

## Homebrew Installation
1.安装brew
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
2.安装php的源
```
brew tap homebrew/php
```
3.更新
```
brew update
```

## Apache Installation
苹果默认安装了Apache服务器，但是删除了其中的一些脚本，因此我们要用brew安装Apache
1.首先停用并删除苹果自启动Apache的脚本
```
sudo apachectl stop
sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null
```
2.安装新的
```
brew install httpd
```
3.让我们安装的Apache auto-start
```
sudo brew services start httpd
```
4.此时安装的Apache，通过8080端口[访问](http://localhost:8080)，操作Apache命令为
```
sudo apachectl start
sudo apachectl stop
sudo apachectl -k restart
```
5.tips
Apache安装在/usr/local目录下面，配置目录就是/usr/local/etc/httpd, log的目录为/usr/local/var/log/httpd。
给我感觉就是Mac的/usr/local目录就相对于ubuntu的根目录。

## Apache Configuration
1.Basic Configuration
配置文件为 /usr/local/etc/httpd/httpd.conf
```
# 配置80端口，将8080改为80端口访问
Listen 80
# 配置服务器访问文件目录
DocumentRoot /Users/your_user/Sites
# 配置网站目录
<Directory /Users/your_user/Sites>
# 配置服务器访问权限
AllowOverride All
# 启用 Apache rewrite
LoadModule rewrite_module lib/httpd/modules/mod_rewrite.so
# 配置服务器访问用户和组
User your_user
Group staff
# 配置服务器名
ServerName localhost
```
2.Advance configuration
配置多域名多站点
- 首先配置 /usr/local/etc/httpd/httpd.conf
```
LoadModule vhost_alias_module lib/httpd/modules/mod_vhost_alias.so
Include /usr/local/etc/httpd/extra/httpd-vhosts.conf
```
- 配置多站点，配置文件 /usr/local/etc/httpd/extra/httpd-vhosts.conf,多站点配置文件会覆盖原有的localhost，所以需要重新定义
```
<VirtualHost *:80>
    DocumentRoot "/Users/your_user/Sites"
    ServerName localhost
</VirtualHost>

<VirtualHost *:80>
    DocumentRoot "/Users/your_user/Sites/grav-admin"
    ServerName grav-admin.test
</VirtualHost>
```

## PHP Installation
1. 安装各版本的php
brew unlink是将当前php的执行文件从bin目录中取消，即不能执行该版本php，相对的brew link将某个版本的php执行文件添加到bin目录，可执行
```
brew install php56 --with-httpd
brew unlink php56
brew install php70 --with-httpd
brew unlink php70
brew install php71 --with-httpd
brew unlink php71
brew install php72 --with-httpd
```
2. php.ini文件
/usr/local/etc/php/5.6/php.ini
/usr/local/etc/php/7.0/php.ini
/usr/local/etc/php/7.1/php.ini
/usr/local/etc/php/7.2/php.ini
3. xdebug安装
```
brew install php71-xdebug --build-from-source
# 编辑 /usr/local/etc/php/7.0/conf.d/ext-xdebug.ini，添加
xdebug.remote_enable=1
```
4. tips
php的extension目录在/usr/local/etc/php/7.1/conf.d目录下
/usr/local/Cellar目录是php的安装目录，/usr/local/opt目录则是软连接指向该目录，Mac下的软件安装都是类似这样的存放

## 启用Apache中的php
```
LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
#LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so
#LoadModule php7_module    /usr/local/opt/php72/libexec/apache2/libphp7.so
```
配置使用某个版本的php，当然这里的php执行路径可能有些小问题，如果有问题，可以自己在/usr/local/opt/对应的php版本中找，我的php71版本，执行文件为 /usr/local/opt/php@7.1/lib/httpd/modules/libphp7.so

修改可执行文件格式
```
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
```
重启Apache，查看phpinfo

## sphp 切换Apache中的可执行php版本
1.安装
```
curl -L https://gist.github.com/w00fz/142b6b19750ea6979137b963df959d11/raw > /usr/local/bin/sphp
chmod +x /usr/local/bin/sphp
```
2.切换php
```
sphp 70
sphp 71
...
```
切换可能会有问题，切换原理应该就是修改Apache配置文件中的loadModule中的可执行php，可是有时候路径会有问题，so。。失败了就自己手动去改吧

## Mariadb
Mysql是Oracle的了，所以安装了替代品mariadb
1.安装
```
brew install mariadb
```
2.启动
```
brew services start mariadb
```
3.设置一些安全选项，如密码设置，访问设置等，执行以下命令，按照提示操作即可
```
/usr/local/bin/mysql_secure_installation
```

## Dnsmasq 安装配置
Dnsmasq可以自动的将某一类域名映射到某个ip，eg(\*.test 映射到 127.0.0.1)
1.安装
```
brew install dnsmasq
```
2.配置
```
echo 'address=/.test/127.0.0.1' > /usr/local/etc/dnsmasq.conf
```
3.启动
```
sudo brew services start dnsmasq
sudo mkdir -v /etc/resolver
sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/test'
```
ping helloworld.test会返回127.0.0.1

## SSL
1.开启Apache扩展，在/usr/local/etc/httpd/httpd.conf中
```
LoadModule socache_shmcb_module lib/httpd/modules/mod_socache_shmcb.so
...
LoadModule ssl_module lib/httpd/modules/mod_ssl.so
...
Include /usr/local/etc/httpd/extra/httpd-ssl.conf
```
2.修改/usr/local/etc/httpd/extra/httpd-ssl.conf
```
Listen 443
...
<VirtualHost _default_:443>

#   General setup for the virtual host
#DocumentRoot "/usr/local/var/www"
#ServerName www.example.com:443
```
3.修改/usr/local/etc/httpd/extra/httpd-vhosts.conf
```
<VirtualHost *:443>
    DocumentRoot "/Users/your_user/Sites"
    ServerName localhost
    SSLEngine on
    SSLCertificateFile "/usr/local/etc/httpd/server.crt"
    SSLCertificateKeyFile "/usr/local/etc/httpd/server.key"
</VirtualHost>
```
4.Certificates
生成证书，执行命令后会提示操作，按照命令回答，ServerName要填写你自己的域名
```
cd /usr/local/etc/httpd
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt
# 查看Apache配置是否正确
sudo apachectl configtest
```
重启Apache


