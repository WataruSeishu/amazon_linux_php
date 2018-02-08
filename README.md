# README

# VagrantインストールからRuby、PHPのインストールまで   

## 動作環境構築について【Vagrant】

## 最新のAmazonLinuxのboxを持ってくる（Virtualbox Only）

```
vagrant init mvbcoding/awslinux
vagrant up --provider virtualbox
vagrant ssh
```

## Amazon Linuxへパッケージインストール

* gitインストール。

※共有フォルダを使う場合もrootユーザーで行う
```
$ sudo su -
# yum -y install git
```

## Apache、phpインストール
rootユーザーで実行
```
# yum install --enablerepo=remi,remi-php71 php php-devel php-mbstring php-pdo php-gd php-xml php-mcrypt
```

## インストール確認
```
# rpm -qa | grep php
# php --version
```

## php.iniを編集
```
# vi /etc/php.ini

date.timezone = "Asia/Tokyo"

mbstring.language = Japanese
mbstring.internal_encoding = UTF-8
mbstring.http_input = UTF-8
mbstring.http_output = pass
mbstring.encoding_translation = On
mbstring.detect_order = auto
mbstring.substitute_charset = none
```

## httpd起動設定
```
# sudo chkconfig httpd on
# sudo chkconfig --list httpd
```

# DB周辺
## MariaDB

※rootユーザーのまま行う
※共有フォルダを使う場合もrootユーザーで行う

```
# vi /etc/yum.repos.d/MariaDB.repo

# MariaDB 10.1 CentOS repository list - created 2016-03-13 07:22 UTC
# http://mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos6-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

```
# yum install -y MariaDB-server MariaDB-client
# /etc/init.d/mysql start
初期設定
# mysql_secure_installation
# chkconfig mysql on
```

## webappユーザの作成
※同期フォルダを使う場合はこの手順は飛ばしwebappユーザーとなっている部分をvagrantユーザーに読み替えること
```
# cp -p /etc/passwd /etc/passwd.ORG
# cp -p /etc/shadow /etc/shadow.ORG
# cp -p /etc/group  /etc/group.ORG
# groupadd webapp
# useradd webapp -g webapp -d /home/webapp -s /bin/bash
# id webapp
uid=501(webapp) gid=501(webapp) groups=501(webapp)
```

## laravelインストール（別のFWを利用したい場合はそちらの手順を利用）
```
# curl -sS https://getcomposer.org/installer | php

# mv composer.phar /usr/local/bin/composer
# export PATH=$PATH:$HOME/.composer/vendor/bin

# composer global require "laravel/installer"
```
  

## webappユーザでphpソースの作成（wip）

```
// webappユーザになる
# sudo su - webapp

// アプリケーションの作成
# cd /var/www/html/
# laravel new blog

# cd blog
# php artisan --version
Laravel Framework X.X.XX

// Laravelのおまじない
# chmod -R 777 storage
# chmod -R 777 bootstrap/cache
```

ブラウザで http:// Public IPアドレス:3000 にアクセスしてrailsの初期画面が表示されたらOK。

## Apacheとphpの接続

## httpd.conf編集とApacheの再起動
```
# sudo vi /etc/httpd/conf/httpd.conf

DocumentRoot "/var/www/html/blog/public" ## 例

# service httpd restart
```

