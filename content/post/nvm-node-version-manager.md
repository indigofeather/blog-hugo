---
title: NVM - Node 版本管理
slug: nvm-node-version-manager
date: 2013-10-05
tags:
- node.js
- 程式筆記
thumbnailImagePosition: left
thumbnailImage: /images/nodejs.png
---

<strike>俗話說得好</strike>：Ruby 有 RVM，Node 有 NVM（喂！），所以今天要來稍微聊一下 `NVM` - Node Version Manager。

<!--more-->

{{< image classes="fig-100" src="/images/nodejs.png" >}}

## 安裝
### 移除舊有安裝
如果你已經先安裝過了，請先移除現有版本。

```bash
sudo apt-get remove nodejs
```

### 安裝 NVM
https://github.com/creationix/nvm

```bash
# 2013-10-16 修正：調整語法執行順序
git clone https://github.com/creationix/nvm.git ~/.nvm
NVM_DIR=~/.nvm
printf "\n\n# NVM\nNVM_DIR=~/.nvm\nsource ~/.nvm/nvm.sh\n[[ -r $NVM_DIR/bash_completion ]] && . $NVM_DIR/bash_completion" >> ~/.bashrc
source ~/.nvm/nvm.sh
```
這裡我把 `bash_completion` 整進來了。

## 使用
### 安裝 Node 指定版本
```bash
nvm install v0.10
```
將會安裝 `v0.10.*` 版，目前是 `v0.10.20`

### 使用指定版本
```bash
nvm use 0.10
```

### 列出遠端可安裝版本
```bash
nvm ls-remote
```

### 指定預設版本
如果不想每次打開終端機都要 `nvm use`，可以指定預設版本
```bash
nvm alias default 0.10
```

### 指定使用特定版本執行
```bash
nvm run 0.10 app.js
```

### 轉移已安裝全域的套件到目前版本
```bash
nvm copy-packages 指定版本
```

## 後記
這樣再也不會踩到 `sudo npm install [package]` 之後可能的權限地雷了。
