---
layout: post
title: '[Android] UiAutomator'
date: 2015-04-29 01:21
comments: true
categories: Android
tag:
	- UiAutomator
	- UI testing
	- Android studio
---
最近公司在跑自動化測試的internal project. 

android 系統本身有提供仿真人點擊 Ui 的 class, UiAutomator
使用java , 程式化的模擬

# Android Studio
我是使用android studio 作為開發的IDE.

1. 先 New 一個project, 要哪一種Activity 都沒差 
2. 修改 在 Project 根目錄 下的build.gradle
修改如下：
```script build.gradle
apply plugin: 'com.android.application'

android {
    compileSdkVersion 19
    buildToolsVersion "22.0.1"
    
    defaultConfig {
        applicationId "com.example.alumincan.mtbf"
        minSdkVersion 19         // Notice: 最少是18, (android4.3)
        targetSdkVersion 19
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    packagingOptions {
        exclude 'LICENSE.txt'   //Duplicate files copied
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    // Testing-only dependencies
    androidTestCompile('com.android.support.test:testing-support-lib:0.1')
    // UiAutomator Testing
    androidTestCompile('com.android.support.test.uiautomator:uiautomator-v18:2.0.0')
}

```

我是在AndroidTrst中建立一個新的test class.

```java MediaTest.java
public class MediaTester extends InstrumentationTestCase {
    private final String TAG = "MediaTester";
    private Context mContext;
    private UiDevice mDevice;
    
    @Override
    protected void setUp() {
        try {
            super.setUp();
            mContext = this.getInstrumentation().getContext();
            mDevice = UiDevice.getInstance(getInstrumentation());

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public void testRecordAudio() {
        mDevice.pressHome();
        Intent intent = new Intent();
        intent.setClassName("com.android.soundrecorder", 
                        "com.android.soundrecorder.SoundRecorder");
        intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK | Intent.FLAG_ACTIVITY_NEW_TASK);
        mContext.startActivity(intent);

        UiObject recorder = new UiObject(new UiSelector().
                       resourceId("com.android.soundrecorder:id/recordButton"));
        UiObject stop = new UiObject(new UiSelector().
                       resourceId("com.android.soundrecorder:id/stopButton"));
        try {
            recorder.click();
            Thread.sleep(1000 * 5);
            stop.click();
            Thread.sleep(1000 * 1);
        } catch (UiObjectNotFoundException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```
上述是sample,
簡單的說就是讓程式找到目前screen上UI元件, 取得並且簡單的控制.
Ex:
```
UiObject recorder = new UiObject(new UiSelector().
                       resourceId("com.android.soundrecorder:id/recordButton"));```
可以透過UiSelector 找尋元件的 text, resourceID and content-desc.
找到後就可以使用Object控制

如何取得元件的text? resourceID? or content-desc?
請到sdk/tools
執行 **uiautomatorviewer**

![UiAutomatorViewer](http://imgur.com/ZEjI9SS.png)
點擊 左上方紅色框框處
你會得到下圖
![UiSeletor](http://i.imgur.com/0AkPuOb.png)

此時就可以知道該元件的個參數值為何.

