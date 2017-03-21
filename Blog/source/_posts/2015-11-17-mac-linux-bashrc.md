---
layout: post
title: '[Mac] [Linux] .bashrc'
date: 2015-11-17 01:03
comments: true
categories: Linux
tag:
	- bash_rc
	- bash_profile
---
在 linux 與 Mac OS X 都可以在
***.bashprofile*** 和 ***.bashrc*** 設定環境變數與 alias，但是這兩個檔案到底差別為何？

當你經由 console 登入（輸入帳號密碼)，或是ssh登入時 ，系統透過 `.bash_profile` 執行相關的設定動作。
而 .bashrc 則是讓非登入的操作命令使用

原則是，當你想要只有登入之後才使用的設定，請放在 .bash_profile
在 Mac OS X 則是用 `.bash_profile` 取代 `.bashrc` 不需要使用 `.bashrc`

1. Create bash_profile at ~/
```script
sudo vim ~/.bash_profile
```
```bash
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
```

2. 單純導入 ll 指令
```bash
#alias
alias ll='ls -lG'
```
3. 以後想到什麼還可以加進去嚕
