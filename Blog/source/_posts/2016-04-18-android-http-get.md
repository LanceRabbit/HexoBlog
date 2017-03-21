---
layout: post
title: '[Android] Http GET & Post'
date: 2016-04-18 20:55
comments: true
categories: Android
tag:
	- Http methods
	- get
	- post
---
[掃除蜘蛛網]
因為轉型作別的案子..  開始要碰很久沒碰的APP
所以  照舊  先把新看的舊東西寫出來
#### Android using Http GET & POST:

First: AndroidManaifests.xml
Please add user-permission Internet in Manifests.


```xml AndroidManaifests.xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.xxx.httpposttowebservice">
    <uses-permission android:name="android.permission.INTERNET" />

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

In android M, using `org.apache.http.legacy`
```gradle build.gradle
android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"
    useLibrary 'org.apache.http.legacy'


```
Before, Make sure your device has already connecting to wifi or internet.
# GET
1. `import import java.net.HttpURLConnection;`
2.  check yout web url `String mWeatherUrl = "http://api.openweathermap.org/data/2.5/find?lat=55.5&lon=37.5&cnt=10"; //openweathermap.org`
3. open connect & read information. 

```java MainActivity.java
HttpURLConnection conn = null;
try {
    String mWeatherUrl = "http://api.openweathermap.org/data/2.5/find?";//"lat=55.5&lon=37.5&cnt=10" openweathermap.org
    
    URL url = new URL(mWeatherUrl);
    conn = (HttpURLConnection) url.openConnection(); // open connect
    conn.setRequestMethod("GET");										 // using GET method
    int responseCode = conn.getResponseCode();       // responseCode will return what eror.
    BufferedReader in = new BufferedReader(
    new InputStreamReader(conn.getInputStream()));   //read your data
    String inputLine = "";
    StringBuffer response = new StringBuffer();
    while ((inputLine = in.readLine()) != null) {
           response.append(inputLine);
    }
    in.close();
    Log.d(TAG, response.toString());
} cache (Exception e) {
    e.printStackTrace();
} finally {
    if (conn != null) conn.disconnect();               //make sure you disconnnect
}

```

# POST
```java MainActivity.java
import javax.net.ssl.HttpsURLConnection;         // import 


String webService = "https://webservice.com.tw;  //replace your web url
        String ret = "";
        String apikey = "api_key";							 // if your service need api, replace it.
        HttpsURLConnection conn = null;
        try {
            URL url = new URL(webService);
            JSONObject query = getJsonObj();
            conn = (HttpsURLConnection) url.openConnection();
            Log.d(TAG, query.toString());

            conn.setRequestMethod("POST");					//using POST

            conn.setRequestProperty("Content-Type", "application/json");	//check with your service
            conn.setRequestProperty("Accept", "application/json");      //check with your service
            conn.setFixedLengthStreamingMode(query.toString().getBytes().length);
            conn.setRequestProperty("Authorization", apikey);  //check with your service
            conn.connect();
            OutputStream os = conn.getOutputStream();
            os.write(query.toString().getBytes());
            os.flush();

            InputStream in = new BufferedInputStream(conn.getInputStream());		//get information
            BufferedReader reader = new BufferedReader(new InputStreamReader(in));
            StringBuilder result = new StringBuilder();
            String line = "";
            while ((line = reader.readLine()) != null) {
                result.append(line);
                Log.d(TAG, line);
            }
            Log.d(TAG, in.toString());

        } catch (Exception e) {
            Log.d(TAG, "Log = " + e.toString());
            e.printStackTrace();
        }
        finally {
            if (conn != null) conn.disconnect();
        }
```





