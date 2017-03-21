---
layout: post
title: '[Android] icu4c'
date: 2014-12-05 11:46
comments: true
categories: Android
tags: 
	- icu4c
	- Locales
---
在工作上必須碰到跟語言相關的Function
其中一個就是ICU. (絕對不是加護病房)

ICU: International_Components for Unicode
International Components for Unicode (ICU) is an open source project of mature C/C++ and Java libraries for Unicode support, software internationalization, and software globalization. ICU is widely portable to many operating systems and environments. It gives applications the same results on all platforms and between C, C++, and Java software. The ICU project is sponsored, supported, and used by IBM and many other companies.[1]

http://en.wikipedia.org/wiki/International_Components_for_Unicode
詳細可以參考上面wiki網址，很遺憾沒有繁體中文的wiki. 連同繁體中文的介紹也很少 QQ
------------------------------------------------------------------------

首先是環境需求首先是環境需求：
因為文件未提起，所以我不知道要準備哪些東西
基本上能build android 的codebase 應該就可以了!!!! (完全的不負責任)

Issue 描述：
choose language used “Burma”  is worng, please change to “”Myanmar”


![icu_1.png](/images/icu4c_setting_before.png)
大意就是 - 上面這張圖的紅色框框字是錯的.  我希望改成下圖框框內的文字
![icu_2.png](/images/icu4c_setting_after.png)

很簡單吧！？

首先，這是緬甸語. 
可以下達`adb shell getprop | grep persist `確認語系.  
我確認過為語系為 `my_ZG`  


所以修改一下這個檔案`external/icu4c/data/lang/my_ZG.txt`	

找到my 發現旁邊的文字跟第一張圖相同.  然後  修改阿 xD
改好之後

## 產生一個icuBuild的資料夾在external/icu4c 並到路徑底下
```
mkdir external/icu4c/icuBuild
cd external/icu4c/icuBuild
```
## 產生make 文件，然後make 

```
.././runConfigureICU Linux
make –j2

```
## 確認
Make後在external/icu4c/icuBuild/data/out/tmp/ 產生一個 icudt48l.dat. 可以將此檔案push到手機中 測試是否修改正確。
```
cd external/icu4c/icuBuild/data/out/tmp/
adb remount
adb push icudt48l.dat system/usr/icu
adb reboot
```
*icudt48l 會因為icu版本不同而有不同的數字，所以不一定皆為48l.dat*

此時重開機已經將文字修改過
## 上code
修改好了，必須將檔案push到git server上. 請將 external/icu4c/icuBuild/data/out/tmp/icudt48l.dat rename 為 icudt44l-all.dat. 並將此檔案覆蓋至 external\icu4c\stubdata. 請注意，須覆蓋原始檔案.

## 設定環境變數

```
export ANDROID_BUILD_TOP=project_path
```

## 執行script, 重新產生.dat

```
cd external/icu4c/studdata
./icu_dat_generator.py
```

下 ```git status```可以發現icudt48l.dat 已經被修改過.
就直接 ```git commit -a``` 然後push到 server上吧

![icu_3.png](/images/icu4c_git_log.png)
