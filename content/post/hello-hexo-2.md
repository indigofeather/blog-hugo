---
title: Hello Hexo 2.0.0
slug: hello-hexo-2
date: 2013-08-26
tags:
- Hexo
thumbnailImagePosition: left
thumbnailImage: /images/hexo2.png
---

有沒有昨天剛介紹完就立即大升級的八卦……

中文文件連結全部斷了……

<!--more-->

{{< image classes="fig-100" src="/images/hexo2.png" >}}

## 從 1.x 到 2.0.0
### 指令
首先指令多了 alias 及參數
{{< codeblock >}}
#hexo new
hexo n

#hexo generate
hexo g
#	-d --deploy Deploy after generate done
#	-w --watch Watch file changes

#hexo server
hexo s
#	-p --port Override default port
#	-s --static Only serve static files
#	-l --log Enable logger. Override logger format.

#hexo deploy
hexo d
#	--setup	Setup without deployment
#	--generate	Generate before deployment
{{< /codeblock >}}

經目前測試結果，`server` 會自動 watch 檔案變更，這是非常貼心的功能！

新增指令：
{{< codeblock >}}
# Renders files.
hexo render <file1> [file2] ...

# Migrates from other blog systems.
hexo migrate <type>

# Cleans the cache file (db.json) and generated files (public).
hexo clean

# Lists all routes.
hexo list <type>
{{< /codeblock >}}

## `_config.yml` 設定檔變更
{{< codeblock >}}
# 增加
external_link: true # 在新分頁開啟外部連結
multi_thread: true  # 多線程 generate

# 移除
highlight:
  backtick_code_block: true
{{< /codeblock >}}

## light theme 有變更
記得備份自行更改的部份，將新的檔案覆蓋過後再修改。

## tag-plugins 有比較詳細的介紹了
<http://zespia.tw/hexo/docs/tag-plugins.html>

## 再來是自訂部份的修改
### .gitignore
{{< codeblock lang="bash" >}}
.DS_Store
node_modules
## 以下為自訂
.deploy
db.json
public/*
## 2.0
*.log
{{< /codeblock >}}

### Makefile
內部已經提供 `clean` 指令，做以下調整：
{{< codeblock lang="bash" >}}
#!/usr/bin/make -f

all:
	@(echo '可用命令：')
	@(echo '        clean 移除多餘檔案')
	@(echo '        deploy 移除多餘檔案並佈署')

deploy: clean
	hexo deploy --generate

clean:
	rm -rf .deploy
	hexo clean
{{< /codeblock >}}

## 後記
改版就是這麼簡單！

## 延伸閱讀
 - github <https://github.com/tommy351/hexo>
 - 官網 <http://zespia.tw/hexo/>
 - 官網文件 <http://zespia.tw/hexo/docs/>
