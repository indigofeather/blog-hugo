---
title: 自動化更新版本：使用 Gitlab Web Hook
slug: automatic-update-version-with-gitlab-web-hook
date: 2013-10-19
tags:
- Git
- Gitlab
- 程式筆記
thumbnailImagePosition: left
thumbnailImage: /images/gitlab.png
---

先前更新系統我們使用 shell script 寫好語法，在 crontab 中設定排程，一段時間去做更新程式的動作。現在換個角度思考，讓 `git push` 之後觸發更新程式。

<!--more-->

{{< image classes="fig-100" src="/images/gitlab.png" >}}

## 過去的方法
準備一支 `update.sh`
{{< codeblock lang="bash" >}}
#!/bin/bash

cd ~/path/to/project
git remote update -p
git checkout -f origin/master
git submodule update --init
{{< /codeblock >}}

然後在 `crontab` 中每分鐘執行更新

{{< codeblock >}}
* * * * * sh /path/to/update.sh &> /dev/null
{{< /codeblock >}}

這樣做看似正常，但每分鐘全部機器一起詢問 server，似乎不是最佳解，因此尋求其他途徑。

## Git Hook
Git 會在某些特定事件中觸發 hook，如果有需要做的動作，便可在相關位置插入需要的鉤子，而在這邊我們將使用到的是 `post-receive`。
`post-receive` 是在 server 端，每次處理完 push 後觸發。

## Gitlab Web Hook
Gitlab 已經預先綁定了幾個鉤子，它已經為我們處理好處發時機點，並且提供 Web Hook 接口。
因此我們不需要自行修改 `.git/hooks` 中的 `post-receive`，可以直接掛上多個要觸發的動作。
使用 Web Hook 的好處是可以觸發不同服務要做的事，例如觸發持續整合（ci）。

## 資料範例
而 Gitlab Web Hooks 觸發時會發送一個 post 請求到指定服務。
### 資料範例
{{< codeblock lang="json" >}}
{
  "before": "95790bf891e76fee5e1747ab589903a6a1f80f22",
  "after": "da1560886d4f094c3e6c9ef40349f7d38b5d27d7",
  "ref": "refs/heads/master",
  "user_id": 4,
  "user_name": "John Smith",
  "repository": {
    "name": "Diaspora",
    "url": "git@localhost:diaspora.git",
    "description": "",
    "homepage": "http://localhost/diaspora",
  },
  "commits": [
    {
      "id": "b6568db1bc1dcd7f8b4d5a946b0b91f9dacd7327",
      "message": "Update Catalan translation to e38cb41.",
      "timestamp": "2011-12-12T14:27:31+02:00",
      "url": "http://localhost/diaspora/commits/b6568db1bc1dcd7f8b4d5a946b0b91f9dacd7327",
      "author": {
        "name": "Jordi Mallach",
        "email": "jordi@softcatala.org",
      }
    },
    // ...
    {
      "id": "da1560886d4f094c3e6c9ef40349f7d38b5d27d7",
      "message": "fixed readme",
      "timestamp": "2012-01-03T23:36:29+02:00",
      "url": "http://localhost/diaspora/commits/da1560886d4f094c3e6c9ef40349f7d38b5d27d7",
      "author": {
        "name": "GitLab dev user",
        "email": "gitlabdev@dv6700.(none)",
      },
    },
  ],
  "total_commits_count": 4,
};
{{< /codeblock >}}


## 服務端
接下來我們會需要一個服務端來幫我們做後續的事情，這裡以 `example_hook.php` 為範例：
{{< codeblock "example_hook.php" "php" >}}
<?php

// 認證用
$valid_token = 'd49dfa7622681425fbcbdd687eb2ca59498ce852';
$valid_ip = array('127.0.0.1');

$client_token = $_GET['token'];
$client_ip = $_SERVER['REMOTE_ADDR'];

$fs = fopen('./example_hook.log', 'a');
fwrite($fs, 'Request on ['.date("Y-m-d H:i:s").'] from ['.$client_ip.']'.PHP_EOL);

// 認證 token
if ($client_token !== $valid_token)
{
    echo "error 10001";
    fwrite($fs, "Invalid token [{$client_token}]".PHP_EOL);
    exit(0);
}

// 認證 ip
if ( ! in_array($client_ip, $valid_ip))
{
    echo "error 10002";
    fwrite($fs, "Invalid ip [{$client_ip}]".PHP_EOL);
    exit(0);
}

$json = file_get_contents('php://input');
$data = json_decode($json, true);
fwrite($fs, 'Data: '.print_r($data, true).PHP_EOL);
fwrite($fs, '======================================================================='.PHP_EOL);
$fs and fclose($fs);

// 執行上面所述的 update.sh
exec("sh /path/to/update.sh");
{{< /codeblock >}}

**注意檔案權限問題**

## 在 Gitlab 加入 Web Hook
<!--{% img center /img/2013101904.png %}-->

## 測試
{{< codeblock >}}
cd /path/to/project
touch example
git add .
git commit -m 'commit 訊息'
git push origin master
{{< /codeblock >}}

### 看 log
{{< codeblock "example_hook.log" >}}
Request on [2013-10-19 21:21:16] from [114.33.8.18]
Data: Array
(
    [before] => 15b64b74f04128d10ad576c9c139c1a2a61c6761
    [after] => 54484322a10dbfb84c6d1caf201507d0b2b332cd
    [ref] => refs/heads/master
    [user_id] => 1
    [user_name] => User
    [repository] => Array
        (
            [name] => project
            [url] => git@domain.com:user/project.git
            [description] => 
            [homepage] => http://domain.com/user/project
        )

    [commits] => Array
        (
            [0] => Array
                (
                    [id] => 54484322a10dbfb84c6d1caf201507d0b2b332cd
                    [message] => commit 訊息
                    [timestamp] => 2013-10-15T11:16:42+08:00
                    [url] => http://domain.com/user/project/commit/54484322a10dbfb84c6d1caf201507d0b2b332cd
                    [author] => Array
                        (
                            [name] => User
                            [email] => user@email.com
                        )

                )

        )

    [total_commits_count] => 1
)
=======================================================================
{{< /codeblock >}}

## 後記
這邊的例子只是簡單敘述，其他妙用就留待你自己發想了。
