---
layout: post
title: '[Android] enable / disable activity'
date: 2014-10-06 10:58
categories: Android
tag: 
	- Activity
---
Android 內建了很多Activity, 有些是不希望被end user看到
所以有幾種方式可以將Activity不出現在 Home/app list

* 1 AndroidManifest.xml
```xml
<action android:name="android.intent.action.MAIN"  />
<category android:name="android.intent.category.LAUNCHER" />
```
將上述兩個其中一個移除即可				
優點：簡單、易懂
缺點：需要rebuild app. Activity才會出現在app list.
	
* 2 使用PackageManager 的API	
使用PackageManager的API, 可以在程式中設定一個Activity的state為enable or disable.

```java
import android.content.ComponentName;
import android.content.pm.PackageManager;
/*......................................................*/
......
......
PackageManager pm=getPackageManager();
ComponentName cpn = new ComponentName(packageName, ClassName);
pm.setComponentEnabledSetting(cpn, PackageManager.COMPONENT_ENABLED_STATE_ENABLED, 
			PackageManager.DONT_KILL_APP); // enable app
pm.setComponentEnabledSetting(cpn, PackageManager.COMPONENT_ENABLED_STATE_DISABLED, 
			PackageManager.DONT_KILL_APP); // disable app.
.......
.......
```	
上面的例子是使用setComponentEnabledSetting這個API.	
下面的例子則是使用 setApplicationEnabledSetting.		

```java
import android.content.pm.PackageManager;
/*......................................................*/
......
......
PackageManager pm=getPackageManager();

pm.setApplicationEnabledSetting(ActivityName, PackageManager.COMPONENT_ENABLED_STATE_ENABLED, 
			PackageManager.DONT_KILL_APP); // enable app
pm.setApplicationEnabledSetting(ActivityName, PackageManager.COMPONENT_ENABLED_STATE_DISABLED, 
			PackageManager.DONT_KILL_APP); // disable app.
.......
.......
```
[參考連結 - setComponentEnabledSetting](http://developer.android.com/reference/android/content/pm/PackageManager.html#setComponentEnabledSetting(android.content.ComponentName, int, int))
[參考連結 - setApplicationEnabledSetting](http://developer.android.com/reference/android/content/pm/PackageManager.html#setApplicationEnabledSetting(java.lang.String, int, int))	
  


