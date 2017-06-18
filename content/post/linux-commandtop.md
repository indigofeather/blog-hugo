---
title: Linux 命令：top
slug: linux-commandtop
date: 2013-09-01
tags:
- Linux
- 程式筆記
thumbnailImagePosition: left
thumbnailImage: /images/linux.jpg
---

`top` 是常用來查看系統是否負載過重的工具之一，今天就來了解訊息內容。

<!--more-->

{{< image classes="fig-100" src="/images/linux.jpg" >}}

## 開始
### 命令
{{< codeblock >}}
top
{{< /codeblock >}}

### 結果
{{< image classes="fig-100" src="/images/2013090101.png" >}}

### 解析
第一行：`top - 時間 up 系統運行時間, 登入的使用者, 負載（1 分鐘、5 分鐘、15 分鐘）`
第二行：`程序：總數，執行，睡眠，停止，殭屍`
第三行：`Cpu(s): us, sy, ni, id, wa, hi, si, st`

- **us：**使用者空間使用 CPU 百分比
- **sy：**內核空間使用 CPU 百分比
- **ni：**使用者程序空間內改變過優先級的程序使用 CPU 百分比
- **id：**閒置 CPU 百分比
- **wa：**等待 I/O 的 CPU 時間百分比
- **hi：**硬體 IRQ（IRQ 全名為 Interrupt Request，中斷請求的意思）
- **si：**軟體 IRQ
- **st：**Steal Time

第四行：`KiB 記憶體: 總數，使用，閒置，緩衝`
第五行：`KiB 交換空間: 總數，使用，閒置，快取`
程序區：見以下。

### 程序區
預設顯示：`PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND`
按 `f` 選擇顯示內容：
{{< image classes="fig-100" src="/images/2013090102.png" >}}

項目 | 描述
--- | ---
**PID** | 程序 Id
**USER** | 有效的使用者名稱
**PR** | 優先度
**NI** | Nice 值。負值表示高優先度，正值表示低優先度
**VIRT** | 程序使用的虛擬記憶體總量，單位 kb。VIRT=SWAP+RES
**RES** | 程序使用的、未交換出的實體記憶體大小，單位 kb。RES=CODE+DATA
**SHR** | 共享記憶體大小，單位 kb
**S** | 程序狀態。（D=不可中斷的睡眠狀態、R=運行、S=睡眠、T=跟蹤／停止、Z=殭屍程序）
**%CPU** | CPU 時間使用百分比
**%MEM** | 實體記憶體使用百分比
**TIME+** | CPU 時間，單位 1/100 秒
**COMMAND** | 命令名稱／命令列
PPID | 父程序 Id
UID | 有效的使用者 Id
RUID | 真實使用者 Id
RUSER | 真實使用者名稱
SUID | 保存的使用者 Id 
SUSER | 保存的使用者名稱
GID | 群組 Id
GROUP | 群組名稱
PGRP | 程序群組 Id
TTY | 啟動程序的終端名稱。不是從終端啟動的程序則顯示為 ?
TPGID | Tty Process Grp Id
SID | Session Id
nTH | 線程數
P | 最後使用的 CPU，僅在多 CPU 環境下有意義
TIME | CPU 時間，單位秒
SWAP | 虛擬記憶體交換大小，單位 kb。
CODE | 可執行代碼佔用的實體記憶體大小，單位 kb
DATA | 可執行代碼以外的部分佔用的實體記憶體大小，單位 kb
nMaj | 主要頁面錯誤
nMin | 次要頁面錯誤
nDRT | 修改過的頁面數。
WCHAN | 若該程序在睡眠，則顯示睡眠中的系統函數名
Flags | 任務標誌，參考 sched.h
CGROUPS | 控制群組
SUPGIDS | Supp 群組 IDs
SUPGRPS | Supp 群組名稱
TGID | 線程群組 Id

## 使用參數
{{< codeblock >}}
# 每隔 5 秒刷新一次，預設 3 秒
top -d 5

# 監控指定程序
top -p 812, 1281
{{< /codeblock >}}

## 熱鍵
- `P` CPU 降序排序。
- `M` 記憶體降序排序。
- `1` 顯示所有 CPU 負載情況。
- `h` 顯示說明。

## 後記
另外還有一個介面更親切的 htop 可供選擇。
{{< image classes="fig-100" src="/images/2013090103.png" >}}

## 延伸閱讀
- 阮一峰的网络日志
  理解Linux系统负荷 <http://www.ruanyifeng.com/blog/2011/07/linux_load_average_explained.html>
