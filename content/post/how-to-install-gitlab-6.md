---
title: 如何安裝 Gitlab 6
slug: how-to-install-gitlab-6
date: 2013-10-19
tags: 
- Git
- Gitlab
- 程式筆記
thumbnailImagePosition: left
thumbnailImage: /images/gitlab.png
---

Gitlab 一路發展至今已進入了 6 版，在穩定度上也提昇了不少，因此這次要認真介紹如何安裝。

<!--more-->

{{< image classes="fig-100" src="/images/gitlab.png" >}}

## 安裝
今天我們安裝的版本是 `6.1-stable`。

### 升級系統套件
{{< codeblock >}}
apt-get update -y
apt-get upgrade -y
apt-get install sudo -y
{{< /codeblock >}}

### 安裝 vim 並設定為預設編輯器
{{< codeblock >}}
sudo apt-get install -y vim
sudo update-alternatives --set editor /usr/bin/vim.basic
{{< /codeblock >}}

### 安裝所需套件
**如果你已經有自行編譯 redis，請略過 redis-server。**
{{< codeblock >}}
sudo apt-get install -y build-essential zlib1g-dev libyaml-dev libssl-dev libgdbm-dev libreadline-dev libncurses5-dev libffi-dev curl git-core openssh-server redis-server checkinstall libxml2-dev libxslt-dev libcurl4-openssl-dev libicu-dev
{{< /codeblock >}}

### 確認你已經安裝正確版本的 Python
**如果已是 2.5+ 版，請略過。**

{{< codeblock >}}
# 安裝 Python
sudo apt-get install -y python

# 確保 Python 版本是 2.5+（3.x 現在還不支援）
python --version

# 如果是 Python 3 你會需要分開安裝 Python 2
sudo apt-get install python2.7

# 確保你可以藉由 `python2` 存取 Python
python2 --version

# 如果你得到一個 "command not found" 錯誤，建立一個到 python binary 的連結
sudo ln -s /usr/bin/python /usr/bin/python2

# 為了支援 reStructuredText 標記語言，安裝所需套件：
sudo apt-get install python-docutils
{{< /codeblock >}}

### 為了接收 mail 通知，我們需要 mail server，推薦使用 postfix
{{< codeblock >}}
sudo apt-get install -y postfix 
{{< /codeblock >}}

### Ruby
{{< codeblock >}}
# 如果有舊的先移除
sudo apt-get remove -y ruby1.8

# 下載並編譯：
mkdir /tmp/ruby && cd /tmp/ruby
curl --progress ftp://ftp.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p247.tar.gz | tar xz
cd ruby-2.0.0-p247
./configure
make
sudo make install

# 安裝 Bundler Gem：
sudo gem install bundler --no-ri --no-rdoc
{{< /codeblock >}}

### 為 Gitlab 建立 `git` 使用者：
{{< codeblock >}}
sudo adduser --disabled-login --gecos 'GitLab' git
{{< /codeblock >}}

### GitLab shell
{{< codeblock >}}
# 到家目錄
cd /home/git

# Clone gitlab shell
sudo -u git -H git clone https://github.com/gitlabhq/gitlab-shell.git

cd gitlab-shell

# 切換到對的版本
sudo -u git -H git checkout v1.7.1

sudo -u git -H cp config.yml.example config.yml

# 編輯配置並取代 gitlab_url
# 例如 'http://domain.com/'
sudo -u git -H editor config.yml

# 設置
sudo -u git -H ./bin/install
{{< /codeblock >}}

### 設定資料庫
#### MySQL
{{< codeblock >}}
# 安裝資料庫套件
sudo apt-get install -y mysql-server mysql-client libmysqlclient-dev

# 登入 MySQL
mysql -u root -p

# 建立使用者 GitLab
# 變更 $password 為你自己取的密碼
mysql> CREATE USER 'gitlab'@'localhost' IDENTIFIED BY '$password';

# 建立 GitLab production 資料庫
mysql> CREATE DATABASE IF NOT EXISTS `gitlabhq_production` DEFAULT CHARACTER SET `utf8` COLLATE `utf8_unicode_ci`;

# 發放 GitLab 使用者在資料表必要的權限
mysql> GRANT SELECT, LOCK TABLES, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER ON `gitlabhq_production`.* TO 'gitlab'@'localhost';

# 離開資料庫
mysql> \q

# 試著用新使用者登入新資料庫
sudo -u git -H mysql -u gitlab -p -D gitlabhq_production
{{< /codeblock >}}

#### PostgreSQL
{{< codeblock >}}
# 安裝資料庫套件
sudo apt-get install -y postgresql-9.1 libpq-dev

# 登入 PostgreSQL
sudo -u postgres psql -d template1

# 建立使用者 GitLab
# 變更 $password 為你自己取的密碼
template1=# CREATE USER git WITH PASSWORD '$password';

# 建立 GitLab production 資料庫 & 發放所有資料庫的權限
template1=# CREATE DATABASE gitlabhq_production OWNER git;

# 離開資料庫
template1=# \q

# 試著用新使用者登入新資料庫
sudo -u git -H psql -d gitlabhq_production
{{< /codeblock >}}

### 安裝 Gitlab
{{< codeblock lang="bash" >}}
cd /home/git
sudo -u git -H git clone https://github.com/gitlabhq/gitlabhq.git gitlab
cd /home/git/gitlab
sudo -u git -H git checkout 6-1-stable

# 配置
# 複製 GitLab 範例配置
sudo -u git -H cp config/gitlab.yml.example config/gitlab.yml

# 確保變更 "localhost" 為你存放 Gitlab 主機的完整網域名稱
sudo -u git -H editor config/gitlab.yml

# 確保 GitLab 可以寫入 log/ 和 tmp/ 目錄
sudo chown -R git log/
sudo chown -R git tmp/
sudo chmod -R u+rwX  log/
sudo chmod -R u+rwX  tmp/

# 為 satellites 建立目錄
sudo -u git -H mkdir /home/git/gitlab-satellites

# 建立 sockets/pids 目錄並確保 GitLab 可以寫入他們
sudo -u git -H mkdir tmp/pids/
sudo -u git -H mkdir tmp/sockets/
sudo chmod -R u+rwX  tmp/pids/
sudo chmod -R u+rwX  tmp/sockets/

# 建立 public/uploads 目錄，否則備份會失敗
sudo -u git -H mkdir public/uploads
sudo chmod -R u+rwX  public/uploads

# 複製 Unicorn 範例配置
sudo -u git -H cp config/unicorn.rb.example config/unicorn.rb
sudo -u git -H editor config/unicorn.rb

# 為 git 使用者配置 Git 全域設定，在透過網頁編輯很有用
# 根據 gitlab.yml 的設定編輯 user.email
sudo -u git -H git config --global user.name "GitLab"
sudo -u git -H git config --global user.email "gitlab@localhost"
sudo -u git -H git config --global core.autocrlf input

# 資料庫配置
# Mysql
sudo -u git cp config/database.yml.mysql config/database.yml

# PostgreSQL
sudo -u git cp config/database.yml.postgresql config/database.yml

# 根據你的使用者帳號密碼來設定
sudo -u git -H editor config/database.yml

# 讓 config/database.yml 只有 git 可讀
sudo -u git -H chmod o-rwx config/database.yml
{{< /codeblock >}}

### 安裝 Gems
{{< codeblock >}}
cd /home/git/gitlab
sudo gem install charlock_holmes --version '0.6.9.4'

# MySQL（注意，選項說的是 "without ... postgres"）
sudo -u git -H bundle install --deployment --without development test postgres aws

# 或 PostgreSQL（注意，選項說的是 "without ... mysql"）
sudo -u git -H bundle install --deployment --without development test mysql aws
{{< /codeblock >}}

### 初始化資料庫和啟動進階功能
{{< codeblock >}}
sudo -u git -H bundle exec rake gitlab:setup RAILS_ENV=production
{{< /codeblock >}}

### 安裝初始化腳本
{{< codeblock >}}
# 下載腳本
sudo cp lib/support/init.d/gitlab /etc/init.d/gitlab
sudo chmod +x /etc/init.d/gitlab

# 讓 Gitlab 開機時啟動
sudo update-rc.d gitlab defaults 21
{{< /codeblock >}}

### 檢查應用程式狀態
{{< codeblock >}}
sudo -u git -H bundle exec rake gitlab:env:info RAILS_ENV=production
{{< /codeblock >}}

## 啟動你的 Gitlab
{{< codeblock >}}
sudo service gitlab start
# 或
sudo /etc/init.d/gitlab restart
{{< /codeblock >}}

## 確認應用程式狀態
{{< codeblock >}}
sudo -u git -H bundle exec rake gitlab:check RAILS_ENV=production
{{< /codeblock >}}

如果有任何錯誤或問題可在此除錯。

## Nginx 設定
從 gitlab 專案複製出來，並自行修改
{{< codeblock >}}
sudo cp lib/support/nginx/gitlab /etc/nginx/sites-available/gitlab
sudo ln -s /etc/nginx/sites-available/gitlab /etc/nginx/sites-enabled/gitlab
sudo editor /etc/nginx/sites-available/gitlab
{{< /codeblock >}}

## 成果
{{< image classes="fig-100" src="/images/2013101901.png" >}}
{{< image classes="fig-100" src="/images/2013101902.png" >}}
{{< image classes="fig-100" src="/images/2013101903.png" >}}

## 後記
Gitlab 功能持續進化中，期待日後有更亮眼的功能！

## 參考資料
  - [Gitlab 官網](http://gitlabhq.com/)
