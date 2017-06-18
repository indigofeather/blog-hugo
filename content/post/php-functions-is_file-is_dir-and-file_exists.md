---
title: PHP 函式：is_file()、is_dir() 和 file_exists()
slug: php-functions-is_file-is_dir-and-file_exists
date: 2013-09-15
tags:
- 程式筆記
- PHP
thumbnailImagePosition: left
thumbnailImage: /images/php.png
---

最近各大 framework 紛紛有人開 issue 表示為了效能，改用 `is_file()` 來替代 `file_exists()`，那就來簡單探討一下中間的差異。

<!--more-->

{{< image classes="fig-100" src="/images/php.png" >}}

## 略觀
### is_file()
檢查所給名稱是否是檔案。

```php
var_dump(is_file('wtf_426.txt')) . "\n";
var_dump(is_file('faluengong/taidu')) . "\n";
```
輸出：
```php
bool(true)
bool(false)
```
- 詳情參考[官網文件](http://www.php.net/manual/en/function.is-file.php)

### is_dir()
檢查所給名稱是否是目錄。

```php
var_dump(is_dir('wtf_426.txt'));
var_dump(is_dir('faluengong/taidu'));
var_dump(is_dir('..')); // one dir up
```
輸出：
```php
bool(false)
bool(false)
bool(true)
```

- 詳情參考[官網文件](http://www.php.net/manual/en/function.is-dir.php)

### file_exists()
會同時檢查檔案與目錄兩種，但如果是連結會回傳 `false`。

```php
$filename = '/path/to/wtf_426.txt';

if (file_exists($filename)) {
    echo "檔案 $filename 存在";
} else {
    echo "檔案 $filename 不存在";
}
```

- 詳情參考[官網文件](http://www.php.net/manual/en/function.file-exists.php)

## 效能比較
了解基本用法後，接著就針對 `is_file()` 與 `file_exists()` 來做測試。
為了客觀，全部測試都獨立跑。

### 測試一：檔案存在，使用 `is_file`
測試碼：
```php
$path = 'test.php';
$start = microtime(true);
for ($i = 0; $i <= 1000000; $i++) {
    is_file($path);
}
var_dump(microtime(true) - $start);
```
結果：
```
float 3.8660399913788
float 3.8526499271393
float 3.9998660087585
float 3.9967021942139
float 3.9253749847412
```

### 測試二：檔案存在，使用 `file_exists`
測試碼：
```php
$path = 'test.php';
$start = microtime(true);
for ($i = 0; $i <= 1000000; $i++) {
    file_exists($path);
}
var_dump(microtime(true) - $start);
```
結果：
```
float 5.5691380500793
float 5.7048909664154
float 5.6516408920288
float 5.6038320064545
float 5.6260890960693
```

### 測試三：檔案不存在，使用 `is_file`
測試碼：
同測試一。

結果：
```
float 4.9181349277496
float 4.6872041225433
float 4.8384959697723
float 4.8967249393463
float 4.8434829711914
```

### 測試四：檔案不存在，使用 `file_exists`
測試碼：
同測試二。

結果：
```
float 5.5517020225525
float 5.7647449970245
float 5.4528181552887
float 5.834037065506
float 5.4938218593597
```

## 總結
由以上測試可知，檔案存在的情況下，`is_file()`速度大勝，而檔案不存在的情況下，`is_file()`明顯變慢了，而`file_exists()`則穩定不變。

因此，不難了解到為什麼會有前言所提現象，因為核心部份判斷的檔案原則上都是存在的，而`is_file()`在效能上表現更佳，此情境下就適用。。

## 延伸閱讀
- [PHP Micro-Optimizations - file_exists vs is_file (non existing files)](http://micro-optimization.com/file_exists-vs-is_file-non-existing-files)
