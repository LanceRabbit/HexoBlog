---
layout: post
title: '[Android] Get installed APK List'
date: 2015-04-21 10:58
comments: true
categories: Android
tag:
	- PackageManager
---
Using PackageManager to get all application that uses' installed and system native app.

Please import PackageManager and ResolveInfo first:

```java
import android.content.pm.PackageManager;
import android.content.pm.ResolveInfo;
```

New an array-list to store app list:

```java
private PackageManager mPm;

mPm = mContext.getPackageManager();
Intent mIntent = new Intent(Intent.ACTION_MAIN, null);
mIntent.addCategory(Intent.CATEGORY_LAUNCHER);
List<ResolveInfo> mResolveInfos = mPm.queryIntentActivities(mIntent, 0);
```

maybe we can get apps' icon, packageName, className or other information

```java
for (ResolveInfo rlf : mResolveInfos) {
            try {
                Log.d(TAG, rlf.activityInfo.packageName);
                KeyInformation mInf = new KeyInformation();
                ApplicationInfo app = mPm.getApplicationInfo(rlf.activityInfo.packageName, 0);

                mInf.lable = mPm.getApplicationLabel(app).toString();
                mInf.icon = mPm.getApplicationIcon(rlf.activityInfo.packageName);
                mInf.packageName = rlf.activityInfo.packageName;
                mInf.className = app.className;

                mList.add(mInf);
            } catch (PackageManager.NameNotFoundException e) {
                e.printStackTrace();
            } catch (Exception e) {
                e.printStackTrace();
            }
}
```

using adapter to draw a listview will be like this:
![擷取選取區域_001.png](http://user-image.logdown.io/user/10106/blog/9801/post/261312/ffLnmrnmQ9SLeL7anfOP_%E6%93%B7%E5%8F%96%E9%81%B8%E5%8F%96%E5%8D%80%E5%9F%9F_001.png)