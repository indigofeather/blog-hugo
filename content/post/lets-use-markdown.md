---
title: 一起使用 Markdown 吧！
slug: lets-use-markdown
date: 2013-09-08
tags:
- 程式筆記
- Markdown
thumbnailImagePosition: left
thumbnailImage: /images/markdown.png
---

[Markdown](http://daringfireball.net/projects/markdown/) 是輕量級標記語言，非常適合用在文件、紀錄等應用，能讓你專注在內容，而非排版之上。
當然，你需要了解基礎的 XHTML（HTML） 語法。

<!--more-->

{{< image classes="fig-100" src="/images/markdown.png" >}}

## 簡介
{{< blockquote "John Gruber" "http://daringfireball.net/projects/markdown/" >}}
Markdown is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format, then convert it to structurally valid XHTML (or HTML).
{{< /blockquote >}}

由此可知，Markdown 就是「**易讀**」與「**易寫**」，因此在語法上十分簡潔易懂。

{{< blockquote "John Gruber" "http://daringfireball.net/projects/markdown/syntax#html" >}}
Markdown’s syntax is intended for one purpose: to be used as a format for writing for the web.

Markdown is not a replacement for HTML, or even close to it. Its syntax is very small, corresponding only to a very small subset of HTML tags. The idea is not to create a syntax that makes it easier to insert HTML tags. In my opinion, HTML tags are already easy to insert. The idea for Markdown is to make it easy to read, write, and edit prose. HTML is a publishing format; Markdown is a writing format. Thus, Markdown’s formatting syntax only addresses issues that can be conveyed in plain text.

For any markup that is not covered by Markdown’s syntax, you simply use HTML itself. There’s no need to preface it or delimit it to indicate that you’re switching from Markdown to HTML; you just use the tags.
{{< /blockquote >}}

Markdown 不是要來取代 HTML，所以也不會涵蓋所有標籤，不在範圍內的標籤，都可以直接在文件裡面用 HTML 撰寫。不需要額外標註這是 HTML 或是 Markdown；只要直接加標籤就可以了。
## 語法

### 段落、標題、區塊引言
Markdown：
{{< codeblock lang="md" >}}
H1 的標題
====================

H2 的標題
---------------------

撰寫程式時請保持簡單，
簡單就易懂，
易懂就好維護。

大家一起提升程式碼品質，
加油好嗎？

### H3 的標題

> 引言區塊
> 
> 引言第二段
>
> ## 這是引言中的 H2 標題
{{< /codeblock >}}

輸出：

{{< codeblock lang="html" >}}
<h1>H1 的標題</h1>

<h2>H2 的標題</h2>

<p>撰寫程式時請保持簡單，
簡單就易懂，
易懂就好維護。</p>

<p>大家一起提升程式碼品質，
加油好嗎？</p>

<h3>H3 的標題</h3>

<blockquote>
<p>引言區塊</p>
<p>引言第二段</p>
<h2>這是引言中的 H2 標題</h2>
</blockquote>
{{< /codeblock >}}

### 修辭和強調
Markdown：
{{< codeblock lang="md" >}}
這樣寫的話，*不正*。
這樣寫的話，_也不正_。

用兩個星號來**加粗**。
或用兩個底線來__加粗__。
{{< /codeblock >}}

輸出：

{{< codeblock lang="html" >}}
<p>這樣寫的話，<em>不正</em>。
這樣寫的話，<em>也不正</em>。</p>

<p>用兩個星號來<strong>加粗</strong>。
或用兩個底線來<strong>加粗</strong>。</p>
{{< /codeblock >}}

### 清單

#### 無序清單
這裡借用一下天恆君的口頭禪：
Markdown：
{{< codeblock lang="md" >}}
*   消滅
*   消滅
*   再消滅

+   消滅
+   消滅
+   再消滅

-   消滅
-   消滅
-   再消滅
{{< /codeblock >}}

三種都會輸出：

{{< codeblock lang="html" >}}
<ul>
<li>消滅</li>
<li>消滅</li>
<li>再消滅</li>
</ul>
{{< /codeblock >}}

#### 有序清單
這裡借用一個實例，請問下列哪一位不是金光人物：
Markdown：
{{< codeblock lang="md" >}}
1.  千雪孤鳴
2.  問劍孤鳴
3.  競日孤鳴
{{< /codeblock >}}

輸出：

{{< codeblock lang="html" >}}
<ol>
<li>千雪孤鳴</li>
<li>問劍孤鳴</li>
<li>競日孤鳴</li>
</ol>
{{< /codeblock >}}

### 連結
連結有兩種形式，**行內**與**參考**。

Markdown：
行內：
{{< codeblock lang="md" >}}
其實我比較常用的搜尋引擎是 [Google](http://google.com/)。
{{< /codeblock >}}
參考：
{{< codeblock lang="md" >}}
其實我比較常用的搜尋引擎是 [Google][1]。

[1]: http://google.com/
{{< /codeblock >}}

兩種都會輸出：
{{< codeblock lang="html" >}}
<p>其實我比較常用的搜尋引擎是 <a href="http://google.com/">Google</a>。</p>
{{< /codeblock >}}

### 圖片
圖片的語法與連結相似。

Markdown：
行內：
{{< codeblock lang="md" >}}
![alt text](/path/to/img.jpg "選擇性的標題")
{{< /codeblock >}}
參考：
{{< codeblock lang="md" >}}
![alt text][id]

[id]: /path/to/img.jpg "選擇性的標題"
{{< /codeblock >}}

兩種都會輸出：

{{< codeblock lang="md" >}}
<img src="/path/to/img.jpg" alt="alt text" title="選擇性的標題" />
{{< /codeblock >}}

### 程式碼
如果是行內可以用 <code>`</code> 包覆
{{< codeblock lang="md" >}}
使用`<article>`、`<nav>`、`<section>`標籤前還是先了解一下語意吧。
{{< /codeblock >}}

輸出：

{{< codeblock lang="html" >}}
<p>使用<code>&lt;article&gt;</code>、<code>&lt;nav&gt;</code>、<code>&lt;section&gt;</code>標籤前還是先了解一下語意吧。</p>
{{< /codeblock >}}

區塊可以用 4 個空格或 1 個 tab 縮排：
{{< codeblock lang="md" >}}
我們就借用一下霹靂近年來的怪劇名：

    <blockquote>
        <p>轟定干戈</p>
    </blockquote>
{{< /codeblock >}}

輸出：

{{< codeblock lang="html" >}}
<p>我們就借用一下霹靂近年來的怪劇名：</p>

<pre><code>&lt;blockquote&gt;
    &lt;p&gt;轟定干戈&lt;/p&gt;
&lt;/blockquote&gt;</code></pre>
{{< /codeblock >}}

## 參考文件
- Markdown 中文文件 <http://markdown.tw/>
- Markdown 中文文件 Github <https://github.com/othree/markdown-syntax-zhtw>

## 線上工具
<http://dillinger.io/>

## 後記
目前在工作上，只要是顯示文件內容，同時又有多語系的部份，我皆已移植為 markdown 格式，大幅降低修改細項所要付出的維護成本，畢竟，資料源綁在 view 上面也不是辦法！

下一篇，再來談關於 markdown 擴充及現在可見在各語言的實作！
