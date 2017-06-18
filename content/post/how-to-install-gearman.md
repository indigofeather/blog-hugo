---
title: 如何安裝 Gearman
slug: how-to-install-gearman
date: 2013-08-29
tags:
- Gearman
- PHP
- 程式筆記
thumbnailImagePosition: left
thumbnailImage: /images/gearman.png
---

本篇直切主題，僅介紹安裝流程，至於原理留待後續探討。

<!--more-->

{{< image classes="fig-100" src="/images/gearman.png" >}}

## 開始

{{< codeblock >}}
# 加入 PPA
sudo add-apt-repository ppa:gearman-developers/ppa
sudo apt-get update

# 安裝
# 其他相依的會自動選擇
sudo apt-get install gearman libgearman-dev
{{< /codeblock >}}

### 接著我們測試看一下版本
{{< codeblock >}}
gearmand -V
{{< /codeblock >}}
得到以下結果
{{< codeblock >}}
gearmand 1.0.6 - https://bugs.launchpad.net/gearmand
{{< /codeblock >}}

### 看一下說明
{{< codeblock >}}
gearmand -h
{{< /codeblock >}}
得到以下結果
{{< codeblock >}}
Allowed options:

Allowed options:

General options:
  -b [ --backlog ] arg (=32)            Number of backlog connections for 
                                        listen.
  -d [ --daemon ]                       Daemon, detach and run in the 
                                        background.
  --exceptions                          Enable protocol exceptions by default.
  -f [ --file-descriptors ] arg         Number of file descriptors to allow for
                                        the process (total connections will be 
                                        slightly less). Default is max allowed 
                                        for user.
  -h [ --help ]                         Print this help menu.
  -j [ --job-retries ] arg (=0)         Number of attempts to run the job 
                                        before the job server removes it. This 
                                        is helpful to ensure a bad job does not
                                        crash all available workers. Default is
                                        no limit.
  --job-handle-prefix arg               Prefix used to generate a job handle 
                                        string. If not provided, the default 
                                        "H:<host_name>" is used.
  --hashtable-buckets arg (=991)        Number of buckets in the internal job 
                                        hash tables. The default of 991 works 
                                        well for about three million jobs in 
                                        queue. If the number of jobs in the 
                                        queue at any time will exceed three 
                                        million, use proportionally larger 
                                        values (991 * # of jobs / 3M). For 
                                        example, to accomodate 2^32 jobs, use 
                                        1733003. This will consume ~26MB of 
                                        extra memory. Gearmand cannot support 
                                        more than 2^32 jobs in queue at this 
                                        time.
  -l [ --log-file ] arg (=/var/log/gearmand.log)
                                        Log file to write errors and 
                                        information to. If the log-file 
                                        parameter is specified as 'stderr', 
                                        then output will go to stderr. If 
                                        'none', then no logfile will be 
                                        generated.
  -L [ --listen ] arg                   Address the server should listen on. 
                                        Default is INADDR_ANY.
  -P [ --pid-file ] arg (=/var/gearmand.pid)
                                        File to write process ID out to.
  -r [ --protocol ] arg                 Load protocol module.
  -R [ --round-robin ]                  Assign work in round-robin order per 
                                        worker connection. The default is to 
                                        assign work in the order of functions 
                                        added by the worker.
  -q [ --queue-type ] arg (=builtin)    Persistent queue type to use.
  --config-file arg (=/etc/gearmand.conf)
                                        Can be specified with '@name', too
  --syslog                              Use syslog.
  --coredump                            Whether to create a core dump for 
                                        uncaught signals.
  -t [ --threads ] arg (=4)             Number of I/O threads to use. 
                                        Default=4.
  -u [ --user ] arg                     Switch to given user after startup.
  --verbose arg (=ERROR)                Set verbose level (FATAL, ALERT, 
                                        CRITICAL, ERROR, WARNING, NOTICE, INFO,
                                        DEBUG).
  -V [ --version ]                      Display the version of gearmand and 
                                        exit.
  -w [ --worker-wakeup ] arg (=0)       Number of workers to wakeup for each 
                                        job received. The default is to wakeup 
                                        all available workers.

HTTP:
  --http-port arg (=8080) Port to listen on.

Gear:
  -p [ --port ] arg (=4730) Port the server should listen on.

builtin:

libmemcached:
  --libmemcached-servers arg List of Memcached servers to use.

libsqlite3:
  --libsqlite3-db arg                   Database file to use.
  --libsqlite3-table arg (=gearman_queue)
                                        Table to use.

Postgres:
  --libpq-conninfo arg       PostgreSQL connection information string.
  --libpq-table arg (=queue) Table to use.

MySQL:
  --mysql-host arg (=localhost)      MySQL host.
  --mysql-port arg (=3306)           Port of server. (by default 3306)
  --mysql-user arg                   MySQL user.
  --mysql-password arg               MySQL user password.
  --mysql-db arg                     MySQL database.
  --mysql-table arg (=gearman_queue) MySQL table name.
{{< /codeblock >}}

## 搭配一下 MySQL
### 先加資料庫
{{< codeblock lang="bash" >}}
mysql -u root -p -e 'CREATE DATABASE gearman;'
{{< /codeblock >}}

### 修改配置

**許多文章提到修改 `/etc/init.d/gearman-job-server`，但在我的環境中僅有 `/etc/default/gearman-job-server` 有效。**

找到 `PARAMS="--listen=127.0.0.1"`，改為
{{< codeblock lang="bash" >}}
PARAMS="-q mysql --mysql-host=localhost --mysql-user=user --mysql-password=password --mysql-db=gearman --mysql-table=gearman_queue"
{{< /codeblock >}}

### 重啟
{{< codeblock lang="bash" >}}
sudo service gearman-job-server stop
sudo service gearman-job-server start

# 檢查設定是否生效
ps aux|grep gearman
# 結果
gearman  17002  0.0  0.0 487420  2840 ?        Ssl  00:02   0:00 /usr/sbin/gearmand --pid-file=/var/run/gearman/gearmand.pid --user=gearman --daemon --log-file=/var/log/gearman-job-server/gearman.log 
  -q mysql 
  --mysql-host=localhost 
  --mysql-user=user 
  --mysql-password=password 
  --mysql-db=gearman 
  --mysql-table=gearman_queue
{{< /codeblock >}}
記得再檢查 MySQL 裡面是否已正確自動產生 table，沒有的話請再檢查設定！
{{< image classes="fig-100" src="/images/2013082901.png" >}}

## 安裝 PHP Extension
{{< codeblock >}}
sudo pecl install gearman

# 設定 gearman.ini
sudo vim /etc/php5/conf.d/gearman.ini

# 加上
extension=gearman.so

# 重啟 PHP
sudo service php5-fpm restart

# 驗證是否正確啟用
php -i|grep gearman
# 結果
/etc/php5/cli/conf.d/gearman.ini,
gearman
gearman support => enabled
libgearman version => 1.0.6
{{< /codeblock >}}

## 還沒完喔，有小範例

{{< codeblock "worker.php" "php" >}}
<?php
$worker = new GearmanWorker();
$worker->addServer();
$worker->addFunction('sendEmail', 'doSendEmail');
$worker->addFunction('generateReport', 'doGenerateReport');

while($worker->work()) {
    sleep(1);
}
function doSendEmail($job)
{
    $data = unserialize($job->workload());
    print_r($data);

    // 中間很複雜，不要問
    sleep(3);
    echo "真的完成了一個指派發信的動作\n\n";
}
function doGenerateReport($job)
{
    $data = unserialize($job->workload());
    print_r($data);

    // 中間很複雜，你會怕
    sleep(3);
    echo "真的完成了一個指派產生報表的動作\n\n";
}
{{< /codeblock >}}

{{< codeblock "client.php" "php" >}}
<?php

$client = new GearmanClient();
$client->addServer();

// 發信
$email = array(
    'subject'  => '主題',
    'to' => 'someone@exammail.com',
);

// 產生資料報表
$report = array(
    'type' => 'daily',
);

$client->doBackground('sendEmail', serialize($email));
echo "完成了一個指派發信的動作\n";
$client->doBackground('generateReport', serialize($report));
echo "完成了一個指派產生報表的動作\n";
{{< /codeblock >}}

{{< codeblock >}}
# 準備好之後，執行
php worker.php

# 測試 client
php client.php
# 結果
完成了一個指派發信的動作
完成了一個指派產生報表的動作

# 同時看 worker 視窗輸出
Array
(
    [subject] => 主題
    [to] => someone@exammail.com
)
真的完成了一個指派發信的動作

Array
(
    [type] => daily
)
真的完成了一個指派產生報表的動作
{{< /codeblock >}}
到此大功告成。

## 後記
原理分析及詳細使用方法日後再續。（坑）

## 相關連結
- Gearman 官網 - <http://gearman.org/>
- PHP Manual Gearman - <http://php.net/manual/en/book.gearman.php>
- PECL - <http://pecl.php.net/package/gearman>
