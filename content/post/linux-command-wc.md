---
title: Linux 命令：wc
slug: linux-command-wc
date: 2013-08-30
tags:
- Linux
- 程式筆記
thumbnailImagePosition: left
thumbnailImage: /images/linux.jpg
---

使用 Linux 以來，一直都是依據需求去尋找可用指令，偶然間便發現了 `wc`<strike>（不是 water closet）</strike>。

<!--more-->

{{< image classes="fig-100" src="/images/linux.jpg" >}}

## 首先來看一下說明
{{< codeblock >}}
$ wc --help
用法：wc [選項]... [檔案]...
  或：wc [選項]... --files0-from=F
Print newline, word, and byte counts for each FILE, and a total line if
more than one FILE is specified.  With no FILE, or when FILE is -,
read standard input.  A word is a non-zero-length sequence of characters
delimited by white space.
The options below may be used to select which counts are printed, always in
the following order: newline, word, character, byte, maximum line length.
  -c, --bytes            print the byte counts
  -m, --chars            print the character counts
  -l, --lines            print the newline counts
      --files0-from=F    read input from the files specified by
                           NUL-terminated names in file F;
                           If F is - then read names from standard input
  -L, --max-line-length  print the length of the longest line
  -w, --words            print the word counts
      --help     顯示此求助說明並離開
      --version  顯示版本資訊並離開

Report wc bugs to bug-coreutils@gnu.org
GNU coreutils home page: <http://www.gnu.org/software/coreutils/>
General help using GNU software: <http://www.gnu.org/gethelp/>
回報 wc 翻譯錯誤到 <http://translationproject.org/team/>
For complete documentation, run: info coreutils 'wc invocation'
{{< /codeblock >}}

## 實際操作
範例檔案
{{< codeblock "example">}}
96 69
0-0
素肚
{{< /codeblock >}}

### 預設
回應格式`-l -w -m filename` => `行數、字數、byte 數、檔案名稱`
{{< codeblock >}}
$ wc example
 3  4 17 example
{{< /codeblock >}}

### `-c` 取得 byte 數
{{< codeblock >}}
$ wc -c example
17 example
# 6+4+7=17
{{< /codeblock >}}

#### 釋疑
- **空格**算 `1 byte`。
- **換行符號**算 `1 byte`。
- **UTF-8 編碼**算 `3 byte`。

### `-m` 取得字元數
{{< codeblock >}}
$ wc -m example
13 example
# 6+4+3=13
{{< /codeblock >}}

#### 釋疑
- **空格**算 `1 個字元`。
- **換行符號**算 `1 個字元`。
- **UTF-8 編碼**算 `1 個字元`。

### `-l` 取得行數
{{< codeblock >}}
$ wc -l example
3 example
{{< /codeblock >}}

### `-L` 取得最長行的長度
{{< codeblock >}}
$ wc -L example
5 example
# 5、3、4
{{< /codeblock >}}

#### 釋疑
- **空格**算 `長度 1`。
- **換行符號**`不計算`。
- **UTF-8 編碼**算 `長度 2`。

### `-w` 取得字數
{{< codeblock >}}
$ wc -w example
4 example
# 96, 69, 0-0, 素肚 => 4
{{< /codeblock >}}

## 某些使用情境
### 我只想列出總行數，不想要檔案名稱
{{< codeblock lang="bash" >}}
$ cat example | wc -l
3
{{< /codeblock >}}

### 我想批次掃描檔案獲取行數
{{< codeblock >}}
$ find [path/to/dir] -name '*.php' | xargs wc -l
   32 web/xhprof_html/typeahead.php
   91 web/xhprof_html/callgraph.php
   90 web/xhprof_html/index.php
  213 總計
{{< /codeblock >}}

## 後記
簡單介紹到此為止，我們下次見！<strike>（範例檔案好像怪怪的）</strike>
