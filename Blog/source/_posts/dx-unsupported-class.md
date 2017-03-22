---
title: dx unsupported class file version 52.0
date: 2017-03-22 23:23:53
categories: Android
tags: 
	- Eclipse
	- Build
---
# Bug description

 沒想到我還是得用到Eclipse來build Android app ~"~  
 今天在build apk的時候，一直跳出這個錯誤:  
` dx unsupported class file version 52.0`  
先簡單介紹一下目前的設定環境：  

* open-jdk 1.7  
* oracle jdk 6  
* open-jdk 1.8  
* Eclipse Neon

Eclipse的 Java compiler 是 open-jdk 8  
可以正常的build整個project, but .. 無法產生apk. 

Error message 在上面有提到.  

# 解決方式：  
網路上找了很多的解決方法.例如 `clean build`, or 移除所有的jar等候在重新build.  

我後來使用解決的方法為：  
1. Clean all project  
2. 把Eclipse所有的 java compiler切換到 open-jdk 1.7
3. 重新build需要import 的 jar  
4. 然後在build APK  
5. 就不會看到error message了  

# Why java version 1.7

因為我build的Android app 版本為 5.1.   
1.8 not support, 但是不裝又無法開啟eclipse.  
所以在Eclipse中調降compiler版本  

# 參考連結
[Android developers](https://developer.android.com/guide/platform/j8-jack.html)

