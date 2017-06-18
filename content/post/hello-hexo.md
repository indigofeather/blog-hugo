---
title: Hello Hexo
slug: hello-hexo
date: 2013-08-25
tags:
- Hexo
thumbnailImagePosition: left
thumbnailImage: /images/hexo.png
---

`Octopress` 久未更新，產生文章速度也是差強人意，因此轉向尋找 node.js 版本的 blog 系統，正巧發現了 `Hexo`，Hexo 是台灣人的作品，文件上也十分齊全，使用習慣與前者相去不遠，但速度上大勝，因此毫不猶豫直接轉換平台。

<!--more-->

{{< image classes="fig-100" src="/images/hexo.png" >}}

## 安裝
{{< codeblock >}}
npm install -g hexo
{{< /codeblock >}}

## 更新
{{< codeblock >}}
npm update -g
{{< /codeblock >}}

## 寫作指令

### 建立專案
{{< codeblock >}}
hexo init project
cd project
{{< /codeblock >}}

### 建立新文章
#### **post (default)**
{{< codeblock >}}
hexo new "New Post"
{{< /codeblock >}}

#### **link**
{{< codeblock >}}
hexo new link "Link"
{{< /codeblock >}}

範例：
{{< codeblock lang="yml" >}}
layout: link
title: Link
date: 2012-10-23 17:21:18
comments: true
link: http://www.google.com
tags:
- amet
- consectetur
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin lectus ipsum, accumsan sit amet blandit eget, mattis sit amet ante. Vestibulum at congue mauris. Nam sed massa arcu, sed pulvinar nibh. Maecenas eu eros tellus, sed suscipit tellus. Nam volutpat pharetra risus, non posuere arcu venenatis non. Sed euismod, lorem non consectetur bibendum, quam diam porta orci, id gravida mi nunc ac lacus. Donec a odio non purus euismod ornare eget vitae risus. Suspendisse accumsan, lectus at rutrum tempor, tellus felis egestas tortor, sit amet condimentum risus elit et dui.

Nulla est dolor, bibendum eget sagittis eget, cursus in metus. In quis tortor eu purus imperdiet faucibus. Suspendisse metus sem, porttitor tristique porta et, volutpat ut felis. Cras ut orci ac erat viverra viverra. Quisque sit amet ante nec turpis placerat sollicitudin quis ut purus. Phasellus nec velit sem. Donec at lectus nisl. Praesent molestie convallis sagittis. Sed a libero sed diam mollis congue. Nam et nisl ac elit molestie ullamcorper ut ut lacus. Sed mattis dolor et velit viverra hendrerit. Morbi accumsan augue at elit posuere luctus laoreet justo mattis. 
{{< /codeblock >}}

#### **photo**
{{< codeblock >}}
hexo new photo "Gallery"
{{< /codeblock >}}

範例：
{{< codeblock lang="yml" >}}
layout: photo
title: Gallery
date: 2012-12-07 17:21:14
comments: true
photos:
- http://i.minus.com/iJdpkQwzL3ge3.jpg
- http://i.minus.com/i9pb58hIp3cLn.jpg
- http://i.minus.com/iWSMBq2jqOqyD.jpg
- http://i.minus.com/iot1QlEFNNEBf.jpg
tags:
- consectetur
---

Nunc dignissim volutpat enim, non sollicitudin purus dignissim id. Nam sit amet urna eu velit lacinia eleifend. Proin auctor rhoncus ligula nec aliquet. Donec sodales molestie lacinia. Curabitur dictum faucibus urna at convallis. Aliquam in lectus at urna rutrum porta. In lacus arcu, molestie ut vestibulum ut, rhoncus sed eros. Sed et elit vitae risus pretium consectetur vel in mi. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi tempus turpis quis lectus rhoncus adipiscing. Proin pulvinar placerat suscipit. Maecenas imperdiet, quam vitae varius auctor, enim mauris vulputate sapien, nec laoreet neque diam non quam.
{{< /codeblock >}}

## 生成靜態檔案
{{< codeblock >}}
hexo generate
{{< /codeblock >}}

## 啟動伺服器
{{< codeblock >}}
hexo server
{{< /codeblock >}}

## 設定
沒有過多複雜的設定，詳情請見 <http://zespia.tw/hexo/zh-TW/docs/configure.html>。

另外，`theme` 裡面也有設定需要調整。

## 佈署
常見的幾種佈署方式，詳情請見 <http://zespia.tw/hexo/zh-TW/docs/deploy.html>。

## 其他
 - plugins <https://github.com/tommy351/hexo/wiki/Plugins>
 - themes <https://github.com/tommy351/hexo/wiki/Themes>

## 自訂部份
介紹完基本的，再來是我自己的調整。

遠端佈署有部份檔案不須與 source 一起 commit，請視情況自行調整

### .gitignore
{{< codeblock lang="bash" >}}
.DS_Store
node_modules
## 以下為自訂
.deploy
db.json
public/*
{{< /codeblock >}}

### Makefile
簡單，還能更懶。
{{< codeblock lang="bash" >}}
#!/usr/bin/make -f

all:
	@(echo '可用命令：')
	@(echo '        clean 移除多餘檔案')
	@(echo '        deploy 佈署並移除多餘檔案')

deploy:
	rm -rf .deploy
	hexo deploy --generate
	rm -rf .deploy public/*

clean:
	rm -rf .deploy public/*
{{< /codeblock >}}

## 後記
有好用的 blog 系統，才有動力寫文章呀（握）

## 延伸閱讀
 - 官網文件 <http://zespia.tw/hexo/zh-TW/>
