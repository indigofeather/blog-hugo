---
title: MVCSS - 樣式指南（一）基礎知識
slug: mvcss-styleguide-basics
date: 2014-08-26
tags:
- MVCSS
- CSS
- SASS
thumbnailImagePosition: left
thumbnailImage: /images/mvcss.png
---

MVCSS [Modular View CSS]（模組化檢視 CSS）是一個以 `Sass` 為基礎的 CSS 架構，用於建立可預測與可維護的應用程式樣式。

[上一篇](http://blog.ycnets.com/2014/08/24/mvcss-Intro/) 簡單介紹 MVCSS 相關資料，接著進行樣式指南的介紹，作者群以 Sass 縮排語法的角度出發，習慣 Scss 語法的人可能會覺得不太一樣。

<!--more-->

{{< image classes="fig-100" src="/images/mvcss.png" >}}

## 開始

### code standard

#### 屬性按字母順序排列（有 vendor 前綴的屬性按無前綴來排序）

{{< codeblock lang="sass" >}}
// Bad example
.component
  font-style: italic
  -webkit-flex: 1 1 50%
  -ms-flex: 1 1 50%
  flex: 1 1 50%
  border-radius: 5px

// Good example
.component
  border-radius: 5px
  -webkit-flex: 1 1 50%
  -ms-flex: 1 1 50%
  flex: 1 1 50%
  font-style: italic
{{< /codeblock >}}

#### Extends 和 Mixins 應該被放在標準屬性之前

{{< codeblock lang="sass" >}}
// Bad example
.component
  font-style: italic
  @extend .group
  border-radius: 5px
  +transition(opacity 0.2s ease-in-out)

// Good example
.component
  @extend .group
  +transition(opacity 0.2s ease-in-out)
  font-style: italic
  border-radius: 5px
{{< /codeblock >}}

#### 使用兩個空格縮排

{{< codeblock lang="sass" >}}
// Bad example
.component
    font-style: italic

// Good example
.component
  font-style: italic
{{< /codeblock >}}

#### 在 `:` 之後添加一個空格

{{< codeblock lang="sass" >}}
// Bad example
.component
  border-radius:5px

// Good example
.component
  border-radius: 5px
{{< /codeblock >}}

#### 在註解 `//` 之後添加一個空格

{{< codeblock lang="sass" >}}
//Bad example

// Good example
{{< /codeblock >}}

#### 在值中的逗號之後添加一個空格

{{< codeblock lang="sass" >}}
// Bad example
.component
  box-shadow: 0 2px 5px rgba(#000,0.5)

// Good example
.component
  box-shadow: 0 2px 5px rgba(#000, 0.5)
{{< /codeblock >}}

#### 堅持為類別而非 ID 上樣式

{{< codeblock lang="sass" >}}
// Bad example
#header
  ...

// Good example
.header
  ...
{{< /codeblock >}}

#### 盡可能限制巢狀結構

{{< codeblock lang="sass" >}}
// Bad example
.weather
  .cities
    li
      a
{{< /codeblock >}}

層級一多就代表整段程式碼太過依賴 HTML 結構，而且閱讀理解上會有一定程度的困難，複用性低，也容易因為 HTML 結構調整而失效。

這點在一些指南中甚至直接限定最多三層。

### class 順序

 - 按字母順序排列類別，依序是 Component、Structure、Tool、狀態和情境
 - 在每個獨立模組之後應用修飾符類別（modifier classes）

{{< codeblock lang="html" >}}
<div class="g collection collection--1of3 bch mtl tci is-active has-dropdown"></div>
{{< /codeblock >}}

 - `g` - Component
 - `collection`  -  Structure
 - `collection--1of3` - 修飾符類別
 - `bch mtl tci` - Tool，依字母順序排列
 - `is-active` - 狀態
 - `has-dropdown` - 情境

## 後記

相信用實例探討應該有助於了解此篇定義，詳情可再參考[樣式指南 - 基礎知識](http://mvcss.ycnets.com/styleguide/basics/)頁面。

而下一次，我們要探討的是樣式指南中的數字遊戲。

## 參考資料

 - 官網 <http://mvcss.github.io/>
 - 正體中文文件 <http://mvcss.ycnets.com/>
