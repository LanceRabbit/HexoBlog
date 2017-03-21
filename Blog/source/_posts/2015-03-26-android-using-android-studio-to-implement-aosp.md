---
layout: post
title: '[Android] Using android studio to implement AOSP'
date: 2015-03-26 16:27
categories: Android
tag: 
	- Android studio
	- IDE
	- Android open source project
---
## Android Studio:
這個應該就不需要解釋了.
[Download android studio](http://developer.android.com/sdk/index.html)

## AOSP:Android Open Source Project

Google 推出的 android codebase. [介紹](https://source.android.com/)
請安裝好環境並且下載codebase. 

安裝環境教學：[看這邊](https://source.android.com/source/initializing.html)

下載教學： [再看這邊](https://source.android.com/source/downloading.html)


## 步驟


因為codebase 實在龐大
單一修改某之檔案或是一個apk.  我會使用sublime text
修改好後再使用 make 去編譯

但是一邊trace，又要一邊開發新的feature   沒有IDE實在麻煩

所以找到了可以用 android studio 看整包codebase的方法
1. 先build完整包codebase, 先確認codebase 是否有遺漏或是文法錯誤
2. 終端機回到 AOSP的root path, 執行```mmm development/tools/idegen/```
3. build 完成後，請再執行 ```sh ./development/tools/idegen/idegen.sh```

最後會產生 android.iws, android.ipr, android.iml 等檔案
此時請在 android studio 中，選擇 *Open an exists Android Studio project* 
將目錄導到AOSP的root 即可

成功開啟專案，此時andorid studio會進行一場很漫長的indexing過程
就是下午茶的時候啦！！！

 ## 補充
 
 我在執行3的時候，會遇到以下的fail 
 ```
 Exception in thread "main" java.io.FileNotFoundException: ./out/target/product/hammerhead/obj/GYP/shared_intermediates/res.java (Is a directory)
 ```
此時將 *res.java* rename 為 **res.j**   這樣就可以build過了



