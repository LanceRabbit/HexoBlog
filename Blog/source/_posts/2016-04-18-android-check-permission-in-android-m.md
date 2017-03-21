---
layout: post
title: '[Android] Check permission in Android M'
date: 2016-04-18 22:28
comments: true
categories: Adndroid
tag:
	- permission
	- Android M
---
我今天想要使用gps 幫我定位的時候, 
跳出了System.err
java.lang.SecurityException: "gps" location provider requires ACCESS_FINE_LOCATION permission.

```log
04-20 15:34:38.461 11949 12416 W System.err: java.lang.SecurityException: "gps" location provider requires ACCESS_FINE_LOCATION permission.
04-20 15:34:38.461 11949 12416 W System.err: 	at android.os.Parcel.readException(Parcel.java:1620)
04-20 15:34:38.461 11949 12416 W System.err: 	at android.os.Parcel.readException(Parcel.java:1573)
04-20 15:34:38.461 11949 12416 W System.err: 	at android.location.ILocationManager$Stub$Proxy.getLastLocation(ILocationManager.java:717)
04-20 15:34:38.461 11949 12416 W System.err: 	at android.location.LocationManager.getLastKnownLocation(LocationManager.java:1200)
04-20 15:34:38.461 11949 12416 W System.err: 	at com.cei.httpposttowebservice.MainActivity.locationServiceInitial(MainActivity.java:126)
04-20 15:34:38.461 11949 12416 W System.err: 	at com.cei.httpposttowebservice.MainActivity.getWeather(MainActivity.java:112)
04-20 15:34:38.461 11949 12416 W System.err: 	at com.cei.httpposttowebservice.MainActivity.access$200(MainActivity.java:35)
04-20 15:34:38.461 11949 12416 W System.err: 	at com.cei.httpposttowebservice.MainActivity$4.run(MainActivity.java:100)
04-20 15:34:38.461 11949 12416 W System.err: 	at java.lang.Thread.run(Thread.java:818)
```
除了在AndroidManaifests.xml要加入user-permission之外, 還需要主動需求permission.

```xml AndroidManifests.xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.cei.httpposttowebservice">
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

在java程式中主動提出需求  需要permission
```java MainActivity.java
    private static int PERMISSION_REQUEST_CODE = 1;
    //private Location mLocation;
    private static final String[] INITIAL_PERMS = {
        Manifest.permission.ACCESS_FINE_LOCATION,
        Manifest.permission.ACCESS_COARSE_LOCATION
    };
    
       ----------    
       ----------
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();

        if (checkSelfPermission(Manifest.permission.ACCESS_FINE_LOCATION) ==
                PackageManager.PERMISSION_GRANTED &&
                checkSelfPermission(android.Manifest.permission.ACCESS_COARSE_LOCATION) ==
                        PackageManager.PERMISSION_GRANTED) {
            Toast.makeText(this, "BBBB", Toast.LENGTH_LONG).show();

        } else {
            Toast.makeText(this, "AAAAAA", Toast.LENGTH_LONG).show();
            requestPermissions(INITIAL_PERMS,PERMISSION_REQUEST_CODE);
        }
    }
```
不一定要在onCreate requestpermission
