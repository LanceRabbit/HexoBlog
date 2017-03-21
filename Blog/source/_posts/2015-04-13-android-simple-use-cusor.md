---
layout: post
title: '[Android] 簡單的使用 Cusor'
date: 2015-04-13 17:01
comments: true
categories: Android
tag:
	- Cursor
	- SQLite
---
是為了case 需求
希望能聽到某種訊息之後，變更預設的鈴聲. 

已知：預設鈴聲的 檔名 + 路徑

1. 因為依舊是在Settings的程式，以及必須與SettingsProvider 互動，所以先取得provider
```java
ContentResolver cr = mContext.getContentResolver();
```

2. 定義取得的欄位名稱
```java
String[] cols = new String[] {
                MediaStore.Audio.Media._ID,
                MediaStore.Audio.Media.DATA,
                MediaStore.Audio.Media.TITLE,
                MediaStore.Audio.Media.ALBUM
            };
```

3. 產生 query string, 就是向資料庫 query 資料跟數據
```java
String q = MediaStore.Audio.Media.DISPLAY_NAME + "='some.mp3' and IS_MUSIC=1";
```

4. new 一個物件Cusor, 作為query 後資料擺放的object
```java
Cursor cursor = cr.query(MediaStore.Audio.Media.INTERNAL_CONTENT_URI,
                 cols, q, null, null);
```

5. 此時就可以使用 cusor 物件查看取得的訊息
```java
EX: cursor.getString(0);
```

當然程式碼有經過部份的修改，如果想直接copy paste 可能要再做稍微的修正才能使用

