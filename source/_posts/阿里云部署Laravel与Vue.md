---
title: 阿里云部署Laravel与Vue
date: 2020-03-14 22:05:33
tags:
- PHP
- Vue
- Centos
- Laraver
categories:
- 运维
thumbnail: /gallery/阿里云部署Laravel与Vue.jpg
---
## 购买阿里云ecs服务器
## 选择系统Centos 7
## 设置安全组仅开放必须端口
## 准备好域名并设置好解析
## 使用ssh登陆服务器设置新账户并加入sudoers /etc/suders
## 关闭root账户ssh登录并可更改ssh端口 /etc/ssh/ssh_config
    PermitRootLogin yes => no
## 更新yum源（例如：epel,remi） remi依赖于epel,remi 为php与mysql的最新源
* 安装 epel yum install epel-release
* 安装 remi rpm -Uvh https://rpms.remirepo.net/enterprise/remi-release-7.rpm
## 安装php73
## 安装PHP-FPM
## 安装nginx
## 安装Laravel php必须条件与扩展
* PHP >= 7.1.3
* BCMath PHP Extension
* Ctype PHP Extension
* JSON PHP Extension
* Mbstring PHP Extension
* OpenSSL PHP Extension
* PDO PHP Extension
* Tokenizer PHP Extension
* XML PHP Extension
## Laravel 5.8 要求
## 配置Nginx
```
server {
    listen 80;
    # 响应头允许在<frame>中嵌入页面只限本域名下
    add_header X-Frame-Options "SAMEORIGIN";
    # 开启内建的xss过滤，在检测到后禁止加载页面
    add_header X-XSS-Protection "1; mode=block"; 
    # 禁用浏览器对 Content-Type 类型进行猜测的行为
    add_header X-Content-Type-Options "nosniff";

    root /usr/share/nginx/html;
    index index.php index.html index.htm;

    server_name XXX.XXX.XX;
    # Vue等路由框架使用 history模式
    # 跳转https
    return      301 https://;
    location / {
        try_files $uri $uri/ /index.html
    }

    # php 转发
    location / {
        try_files $uri $uri/ /index.php?query_string;
    }

    # php 脚本请求抓发 FastCGI
    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        # FastCGI监听端口
        fastcgi_pass 127.0.0.1:9000;
        # 设置默认首页
        fastcgi_index index.php;
        # 设置脚本文件请求的路径
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        # 引入fastcgi的配置文件
        include fastcgi_params;
    }
}
```
到此已可以正常使用，可以通过ftp传输源码
## 安装git
* yum install git
* 密钥目录 ~/.ssh
* 生成密钥 ssh-keygen -t rsa
## 安装composer
* wget https://dl.laravel-china.org/composer.phar -O /usr/local/bin/composer
* chmod a+x /usr/local/bin/composer
* composer config -g repo.packagist composer https://packagist.laravel-china.org
## 配置ssl
* 购买ssl证书
* 配置nginx ssl相关配置

    .crt文件：是证书文件，crt是pem文件的扩展名

    .key文件：证书的私钥文件

    将证书文件默认放置在/etc/pki/nginx/a.crt

    将私钥文件默认放置在/etc/pki/nginx/private/b.key

    我习惯在/etc/nginx/cert下

```
server {
    # 端口
    listen 443;
    # 访问的域名
    server_name localhost;
    ssl on;
    # 默认网站根目录
    root /usr/share/nginx/html;
    # 证书文件放置目录
    ssl_certificate "/etc/pki/nginx/a.crt";
    # 证书私钥文件放置目录
    ssl_certificate_key "/etc/pki/nginx/private/b.key";
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    # 协议
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    # 默认请求
    location / {
        try_files $uri $uri/ =404;
    }
    # Vue等路由框架使用 history模式
    location / {
        try_files $uri $uri/ /index.html
    }
    # php 转发
    location / {
        try_files $uri $uri/ /index.php?query_string;
    }
    # php 脚本请求抓发 FastCGI
    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        # FastCGI监听端口
        fastcgi_pass 127.0.0.1:9000;
        # 设置默认首页
        fastcgi_index index.php;
        # 设置脚本文件请求的路径
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        # 引入fastcgi的配置文件
        include fastcgi_params;
    }
}
```
## 文件部署
* 创建网站目录 mkdir
* 更改网站目录用户名与用户组为Nginx的用户名与用户组
* 更改php-fpm的用户组为Nginx的一样 使用ps aux |grep php-fpm检测php-fpm的运行用户 
* git 源码
* composer update
* 配置.env
* 更改 storage 目录权限 chmod -R 755
---
* 创建网站目录 mkdir
* 更改网站目录用户名与用户组为Nginx的用户名与用户组
* git dist 注意.gitignore 不要添加dist

> 注意：php 的名称可能是 `php7*` 等其他形式，注意使用 `Tab`。并  `cp` 一个 `php` 命令文件，因为有些服务默认调用的是 `php`。php-fpm 可以使用 systemctl 查询真正服务名称。安装出现其他名称的命令和服务的原因在于软件版本的众多，可以避免覆盖。