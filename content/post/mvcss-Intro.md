---
title: MVCSS - 簡介
slug: mvcss-intro
date: 2014-08-24
tags:
- MVCSS
- CSS
- SASS
thumbnailImagePosition: left
thumbnailImage: /images/mvcss.png
---

MVCSS [Modular View CSS]（模組化檢視 CSS）是一個以 `Sass` 為基礎的 CSS 架構，用於建立可預測與可維護的應用程式樣式。

CSS，因其簡單性，在大規模實作中是一個難於管理的語言。命名、抽象路徑、結構和方法論都是自由形式；MVCSS 尋求為這些類型的專案提供秩序。

<!--more-->

{{< image classes="fig-100" src="/images/mvcss.png" >}}

## 緣起

起初，MVCSS 由 [Nick Walsh](http://twitter.com/nickawalsh) 和 [Drew Barontini](http://twitter.com/drewbarontini) 所建立，而經過反覆演變之後，現在由 [Envy](http://envylabs.com/) 和 [Code School](http://www.codeschool.com/) 兩家公司的前端團隊繼續貢獻。

## 結合各家思想

在方法論上，汲取了 [OOCSS](http://oocss.org/)、[SMACSS](http://smacss.com/)、[BEM](http://bem.info/method) 的思想，同時參考了 [Foundation](http://foundation.zurb.com/)、[Bootstrap](http://getbootstrap.com/)、[SUIT](https://github.com/suitcss/suit)、[inuit.css](https://github.com/csswizardry/inuit.css) 等各式函式庫的想法，詳情可參閱[資源](http://mvcss.ycnets.com/resources/)。


## 主要架構

{{< codeblock >}}
application.sass
foundation/
  _reset.sass
  _helpers.sass
  _config.sass
  _base.sass
  _tools.sass
components/
structures/
vendor/
{{< /codeblock >}}

為了符合架構的主題，應用程式被分為主要三大類：`Foundation`、`Components` 和 `Structures`。

而 `application.sass` 的內容像這樣：

{{< codeblock lang="sass" >}}
// *************************************
//
//   Project Name
//   -> Manifest
//
// *************************************

// -------------------------------------
//   Foundation
// -------------------------------------

@import "foundation/reset"
@import "foundation/helpers"
@import "foundation/config"
@import "foundation/base"

// -------------------------------------
//   Components
// -------------------------------------

// Component imports

// -------------------------------------
//   Structures
// -------------------------------------

// Structure imports

// -------------------------------------
//   Vendor
// -------------------------------------

// Third-party style imports, if needed

// -------------------------------------
//   Foundation - Tools
// -------------------------------------

@import "foundation/tools"

// -------------------------------------
//   Inbox
// -------------------------------------
{{< /codeblock >}}

由上我們可以看到，載入依序是 `Foundation`、`Components`、`Structures`、`Vendor` 和 `Foundation - Tools`，最後有一個 `Inbox` 區塊。

在實際運用上，除了 CSS 維護人員，其他開發人員可能對於 MVCSS 相關定義並不熟悉，但臨時可能有需求必須先 hotfix，這時候 `Inbox` 便派上用場。開發人員可以將臨時的 hotfix code 先放進這個區塊，日後再由CSS 維護人員把 code 整理到適當的位置，這樣一來，開發人員可以不受拘束先行修正。

這個觀念與 [Sass Style Guide](http://css-tricks.com/sass-style-guide/) 中提到的在主結構最後引入一個 `shame`（或是 `dirty`）類似，前提都是在不打亂現有架構的情況下，達成修正目標，讓相關人員後續再行整理。

## 後記

接下來，我將陸續介紹各項組成元素。

## 參考資料

 - 官網 <http://mvcss.github.io/>
 - 正體中文文件 <http://mvcss.ycnets.com/>
