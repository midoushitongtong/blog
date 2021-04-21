---
title: 手动搭建php环境(win系统)
date: 2019-08-11 16:35:35
categories:
  - 开发杂记
---

##### 记录一下手动搭建 apache + php 环境的大致流程（闲得慌系列）

###### 1. 下载 windows 版 apache 启动器

- [下载 windows 版 apache 启动器传送门](https://www.apachehaus.com/cgi-bin/download.plx)
- 选择`64`位或者`32`位的启动器下载即可
- ![](/images/post/2.png)

<!--more-->

###### 2. 下载 windows 版 php

- [下载 windows 版 php 传送门](https://windows.php.net/download)
- 由于我们使用 apache 作为服务器，这里需要选择线程安全的 php 进行下载
- 选择`64`位或者`32`位的线程安全版本下载即可
- ![](/images/post/3.png)

###### 3. 配置 apache

- 将下载的好的 apache 进行解压. 目录根据喜好放置，`目录最好不要带有中文`
- 修改 `conf/httpd.conf` 配置文件
- 文件查找 `Define SRVROOT` ，修改为`apache目录地址`

```bash
Define SRVROOT "C:/Develop/Environment/PHP/Apache_PHP_One/Apache24"
```

- 文件查找 `DocumentRoot`，修改为 `项目存放的目录地址` 也就是 www 目录

```bash
DocumentRoot "C:/Develop/Environment/PHP/Apache_PHP_One/www"
```

- 文件查找 `<Directory />` ，修改目录权限，不然会 403

```bash
<Directory />
    Options Indexes FollowSymLinks
	AllowOverride All
	Require all granted
</Directory>
```

- 文件查找 DirectoryIndex，修改文件解析`优先级`

```bash
DirectoryIndex index.php index.html
```

- 添加如下 3 条配置，添加位置随意

```bash
1. 配置 php7apache2_4.dll 文件的绝对路径, 此文件在php根目录下
LoadModule php7_module "C:/Develop/Environment/PHP/Apache_PHP_One/PHP7/php7apache2_4.dll"

2. 配置php目录位置
PHPIniDir "C:/Develop/Environment/PHP/Apache_PHP_One/PHP7"

3. 配置文件解析，将.php .html后缀的文件交给php去处理
AddHandler application/x-httpd-php .php .html
```

###### 4. 配置 php

- 将下载的好的 php 进行解压. 目录根据喜好放置，`目录最好不要带有中文`
- 复制 `php.ini-development` 文件, 改为文件名称为 `php.ini`
- 修改 `php.ini` 配置文件
- 文件查找 extension_dir，修改 php 扩展目录

```bash
extension_dir = "C:/Develop/Environment/PHP/Apache_PHP_One/PHP7/ext"
```

- 开启扩展，搜索以下配置，将前面的分号删掉，这一步根据自己情况来开启

```bash
extension=bz2
extension=gd2
extension=mbstring
extension=mysqli
extension=pdo_mysql
```

- 文件查找 upload_tmp_dir，修改临时上传目录
- 注意目录需要自己手动创建

```bash
upload_tmp_dir = "C:/Develop/Environment/PHP/Apache_PHP_One/PHP7/php_upload_tmp"
```

- 文件查找 session.save_path，修改 session 数据存放目录
- 注意目录需要自己手动创建

```bash
session.save_path = "C:/Develop/Environment/PHP/Apache_PHP_One/PHP7/php_session_tmp"
```

- 文件查找 date.timezone，修改默认时区

```bash
date.timezone = Asia/Shanghai
```

###### 5. 安装 apache 服务

- 打开命令行窗口进行操作

```bash
进入apache的bin目录
cd Apache24/bin

安装 apache 服务
httpd.exe -k install -n "apache"
```

###### 6. 测试

- 启动 apache 服务器

```bash
net start apache
```

- 浏览器访问http://127.0.0.0

##### apache 多端口虚拟目录映射配置

- 修改 `conf/httpd.conf`，以下为参考配置

```apacheconf
# 监听80端口
Listen 80
# 配置虚拟端口
<VirtualHost *:80>
    ServerName localhost
	# 端口映射的目录
    DocumentRoot "D:/Develop/Environment/PHP/Apache_PHP_One/WWW"
	# 映射目录的权限
	<Directory "D:/Develop/Environment/PHP/Apache_PHP_One/WWW">
		Options Indexes FollowSymLinks
		AllowOverride All
		Require all granted
	</Directory>
</VirtualHost>

Listen 2333
<VirtualHost *:2333>
    ServerName localhost
    DocumentRoot "D:/Develop/Environment/PHP/Apache_PHP_One/WWW"
	<Directory "D:/Develop/Environment/PHP/Apache_PHP_One/WWW">
		Options Indexes FollowSymLinks
		AllowOverride All
		Require all granted
	</Directory>
</VirtualHost>

Listen 9999
<VirtualHost *:9999>
    ServerName localhost
    DocumentRoot "D:\Develop\Environment\PHP\Apache_PHP_One\WWW\student\public"
	<Directory "D:\Develop\Environment\PHP\Apache_PHP_One\WWW\student\public">
		Options Indexes FollowSymLinks
		AllowOverride All
		Require all granted
	</Directory>
</VirtualHost>
```
