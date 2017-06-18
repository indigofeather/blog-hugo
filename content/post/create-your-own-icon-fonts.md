---
title: 建立你自己的 icon fonts
slug: create-your-own-icon-fonts
date: 2013-08-27
tags:
- icon
- fonts
- 前端筆記
thumbnailImagePosition: left
thumbnailImage: /images/icomoon.png
---

今天就來簡單介紹一下如何使用 [IcoMoon](http://icomoon.io/) 及<strike>康熙字典體</strike>建立自己的 icon fonts。

<!--more-->

{{< image classes="fig-100" src="/images/icomoon.png" >}}

## 開始
首先準備你的素材：icon、字型、<strike>康熙字典體</strike>，準備轉成 `svg` 格式。

### 建立外框、轉存 `svg` 格式
{{< image classes="fig-100" src="/images/2013082701.png" >}}

### 選擇上傳的圖，調整名稱
{{< image classes="fig-100" src="/images/2013082702.png" >}}

### 設定關聯的字符
{{< image classes="fig-100" src="/images/2013082703.png" >}}

### 產生的範例頁，使用其中的 css
{{< image classes="fig-100" src="/images/2013082704.png" >}}
{{< codeblock lang="css" >}}
@font-face {
	font-family: 'yc';
	src:url('fonts/yc.eot');
	src:url('fonts/yc.eot?#iefix') format('embedded-opentype'),
		url('fonts/yc.woff') format('woff'),
		url('fonts/yc.ttf') format('truetype'),
		url('fonts/yc.svg#yc') format('svg');
	font-weight: normal;
	font-style: normal;
}

/* Use the following CSS code if you want to use data attributes for inserting your icons */
[data-icon]:before {
	font-family: 'yc';
	content: attr(data-icon);
	speak: none;
	font-weight: normal;
	font-variant: normal;
	text-transform: none;
	line-height: 1;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

/* Use the following CSS code if you want to have a class per icon */
/*
Instead of a list of all class selectors,
you can use the generic selector below, but it's slower:
[class*="icon-yc"] {
*/
.icon-ycmu, .icon-ycfeng, .icon-ycting, .icon-yctao {
	font-family: 'yc';
	speak: none;
	font-style: normal;
	font-weight: normal;
	font-variant: normal;
	text-transform: none;
	line-height: 1;
	-webkit-font-smoothing: antialiased;
}
.icon-ycmu:before {
	content: "\6c90";
}
.icon-ycfeng:before {
	content: "\98a8";
}
.icon-ycting:before {
	content: "\807d";
}
.icon-yctao:before {
	content: "\6fe4";
}
{{< /codeblock >}}

然後在你想要的地方自由運用

### 我就使用在標題了，成果圖！
{{< image classes="fig-100" src="/images/2013082705.png" >}}

## 後記
所以說**康熙字典體**真的是太氾濫啦（喂）
