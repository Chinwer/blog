+++

author = "邱圆辉"

title = "Chevereto安装总结"

date = "2020-07-13"

description = "在服务器上利用Chevereto自建图床"

tags = [ "tutorial", "image hosting", "chevereto" ]

images = ["http://47.94.16.255/images/images/2020/07/18/pic04.jpg"]

+++

### Chevereto介绍

Chevereto是一款私人图床服务器软件，可以进行图片的存储、处理及分发。昨晚折腾了一个小时，终于是把Chevereto在服务器上搭好了，在此做下总结。

### 安装环境

我使用的LAMP环境，具体如下：

- Linux version 4.18.0, CentOS Linux release 8.1.1911
- Apache 2.4.37 (centos)
- Mysql 8.0.17
- PHP 7.2.11

上面几个依赖的安装就不说了，基本上用`yum install xxx`安装就行了。

### Chevereto源码安装

我的网页根目录为apache默认的/var/www/html，因此在此目录下克隆Chevereto的github项目即可。

这里要注意正确设置克隆后的Chevereto文件夹用户权限。如我的apache服务的用户是apache，那么设置Chevereto权限如下：

```bash
sudo chown -R apache:apache /var/www/html/Chevereto
```

同时，需要保证apache用户对此文件夹有写权限：

```bash
sudo chmod -R 700 Chevereto
```

即只有此文件夹的所有者（apache）具有读、写以及执行的权限。

然而实际上发现这样的话打开网页会403 Forbidden，所以直接设置所有人对此目录可读写及执行了（逃~）：

```bash
sudo chmod -R 777 Chevereto
```

### Mysql数据库设置

为了Chevereto能够正常存储图片，需要给它专门创建一个数据库和数据库用户，这里我首先创建一个名为imagesuser的mysql用户以及一个名为images的数据库，然后授权该用户对此数据库拥有所有权限：

1. 以root用户的身份登录到数据库：

```bash
mysql -u root -p
```

2. 创建一个用户名为imagesuser，密码为0924的用户：

```mysql
create user imagesuser identified by 0924;
```

3. 创建一个名为images的数据库：

```mysql
create database images;
```

4. 授权该用户对此数据库拥有全部权限：

```mysql
grant all on images.* to imagesuser
```

### 最大文件大小设置

Chevereto默认的最大图片大小为2MB，这显然太小了，因此首先修改php允许上传的最大文件大小：

```bash
sudo vim /etc/php.ini
```

在这里面找到以下几个设置项并修改即可：

> memory_limit = 128M
>
> upload_max_filesize = 24M
>
> post_max_size = 32M

设置好后重启apache服务：

```bash
sudo systemctl restart httpd	
```

然后进入Chevereto网页管理后台，点击

> dashboard -> settings -> image-upload

修改最大图片大小即可。

### 一些问题

至此理论上来说访问http://[server-url]/Chevereto应该已经能够work了， 但是我实际安装过程中还遇到了以下几个问题：

- 打开网站后出现 <font color=red>`Chevereto can’t create the app/settings.php file. You must manually create this file`</font>

  解决：手动创建文件/var/www/html/Chevereto/app/settings.php并设置权限：

  ```bash
  cd /var/www/html/Chevereto/app
  sudo touch settings.php
  sudo -R 777 settings.php
  ```

- 打开网站后出现<font color=red>`G: Session are not working on this server`</font>

  原因：Chevereto程序没有对session目录的读写权限，session目录为配置`/etc/php-fpm.d/www.conf`下的：

  > php_value[session.save_path] = /var/lib/php/session

  解决：修改此目录的所属用户即用户组为apache用户及用户组即可：

  ```
  cd /var/lib/php
  sudo chown -R apache:apache session
  ```

- 打开网站后出现<font color=red>`The requested URL /install was not found on this server`</font>，

  原因：google发现作者解释：

  > Apache: Check if mod_rewrite is loaded and configured properly (allowOverride).
  > Nginx: Make sure to add the rules that you will find in the download package.

  解决：因此修改apache配置文件`/etc/httpd/conf/httpd.conf`，找到配置项：

  ```
  <Directory /var/www/html>
  	......
  </Directory>
  ```

  将其修改为：

  ```
  <Directory "/var/www/html">
      #
      # Possible values for the Options directive are "None", "All",
      # or any combination of:
      #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
      #
      # Note that "MultiViews" must be named *explicitly* --- "Options All"
      # doesn't give it to you.
      #
      # The Options directive is both complicated and important.  Please see
      # http://httpd.apache.org/docs/2.4/mod/core.html#options
      # for more information.
      #
      Options Indexes FollowSymLinks MultiViews
  
      #
      # AllowOverride controls what directives may be placed in .htaccess files.
      # It can be "All", "None", or any combination of the keywords:
      #   Options FileInfo AuthConfig Limit
      #
      AllowOverride All
  
      #
      # Controls who can get stuff from this server.
      #
      Require all granted
  </Directory>
  ```

至此，整个Chevereto的配置就弄好啦！然后打开网页，会提示你进行一些初始的设置：数据库的连接、图床管理用户的用户名和密码，联系邮箱等等，按照提示输入就可了，最后上一张成果图：

![screenshot](http://47.94.16.255/images/images/2020/07/13/chevereto.png "成果图")

