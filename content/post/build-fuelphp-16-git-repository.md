---
title: 建立一個 FuelPHP 1.6+ 的應用程式 Git Repository
slug: build-fuelphp-16-git-repository
date: 2014-07-16
tags:
- Git
- FuelPHP
- 程式筆記
thumbnailImagePosition: left
thumbnailImage: /images/fuelphp.png
---

隨著 [FuelPHP](http://fuelphp.com/) 版本推進，相關安裝方式亦有所更動，今天我們就針對 1.6+ 以上版本再做說明。
自從 1.7.2 版開始，所有 git submodule 已被移除，改用 composer 統一管理。

<!--more-->

{{< image classes="fig-100" src="/images/fuelphp.png" >}}

## 開始

### git clone 你要的分支版本
{{< codeblock lang="bash" >}}
# 1.7
git clone git@github.com:fuel/fuel.git -b 1.7/master fuelapp
# 1.8-dev
git clone git@github.com:fuel/fuel.git -b 1.8/develop fuelapp
{{< /codeblock >}}

### 重新建立 git repository
{{< codeblock lang="bash" >}}
cd fuelapp
rm -rf .git/
git init
{{< /codeblock >}}

### 選擇你要的 composer 套件
修改 `composer.json`，除去你不需要的 fuel/*：

{{< codeblock lang="json" >}}
"require": {
        "php": ">=5.3.3",
        "composer/installers": "~1.0",
        "fuel/docs": "1.7.2",
        "fuel/core": "1.7.2",
        "fuel/auth": "1.7.2",
        "fuel/email": "1.7.2",
        "fuel/oil": "1.7.2",
        "fuel/orm": "1.7.2",
        "fuel/parser": "1.7.2",
        "fuelphp/upload": "2.0.1",
        "monolog/monolog": "1.5.*",
        "michelf/php-markdown": "1.4.0"
    },
{{< /codeblock >}}

### composer
{{< codeblock lang="bash" >}}
php composer.phar install
# 日後可用 update
php composer.phar update
# 必要時升級 composer
php composer.phar self-update
{{< /codeblock >}}

### 最後 commit
{{< codeblock lang="bash" >}}
git remote add origin [path/to/your/remote/repository]
git checkout -b master
git add .
git commit -m 'init'
git push origin master
{{< /codeblock >}}

### 附上 Nginx 設定
{{< codeblock lang="bash" >}}
server {
    listen 80;
    server_name fuelapp;
    access_log /home/lance/fuelapp/fuel/app/logs/access.log;
    error_log  /home/lance/fuelapp/fuel/app/logs/error.log;
    root /home/lance/fuelapp/public;

    location / {
        index  index.php index.html index.htm;
        if (!-e $request_filename) {
            rewrite ^.*$ /index.php last;
            break;
        }
    }
    location ~ \.php$ {
      # 視環境調整
      # fastcgi_pass   unix:/var/run/php5-fpm.sock;
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index  index.php;
      fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
      include        fastcgi_params;
    }
    location ~ /\.ht {
            deny  all;
    }
}
{{< /codeblock >}}

## 小禮物
提供完成品，請自行取用
https://github.com/indigofeather/fuelapp

## 後記
1.x 最後一版 1.7 已推出，就讓我們繼續期待 FuelPHP 2.0！
