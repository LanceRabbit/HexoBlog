---
layout: post
title: '[Android] sendStickyBroadcastAsUser'
date: 2015-09-02 16:13
comments: true
categories: Android
tag: 
	- Issue analysis
	- AudioService
	- StickyIntent
---
參考連結：[sendStickyBroadcastAsUser](http://developer.android.com/reference/android/content/ContextWrapper.html#sendStickyBroadcastAsUser(android.content.Intent, android.os.UserHandle))

Issue:
遇到一個詭異的issue. 
前鎮子同事在Service寫了一個Broadcast receiver, 專門收耳機拔插的intent.
收到此intent時，便會跳出一個dialog. 提示使用者 ooxx

客戶回報：淋背沒差耳機他也會跳出來
同事反應：謀哩洗勒工瞎小
但是實際測試: dialog會一直跳出來  跳出來  跳出來
客戶就會覺得：你很煩 你很煩 你很煩
廣告一樣：一直玩 一直玩 一直玩

分析Log: 
Broadcast receiver 確實有收到intent
但是耳機未插拔: root cause -> 七月到了.(誤)

看其他的Log, 發現該Service時常被kill 然後又重啟.(LowMemoryKiller)

改從其他地方trace, 發現插拔耳機 最後會到 AudioService.java
```java
private void sendStickyBroadcastToAll(Intent intent) {
    final long ident = Binder.clearCallingIdentity();
    try {
        mContext.sendStickyBroadcastAsUser(intent, UserHandle.ALL);
    } finally {
        Binder.restoreCallingIdentity(ident);
    }
}
```

我把  ```sendStickyBroadcastAsUser```  換成 ```sendBroadcast```
issue 就不會在產生了

```sendStickyBroadcastToAll``` 是一種android 為了預防app 或是 service漏聽了重要的intent所設計機制
當intent 透過這種方式送出後, intent 內容會keep在某個記憶體位址中. 
一旦有新的 receiver register, intent就會直接送到該receiver.
這種方式大部分是語系統相關的intent Ex:Battery ,earPhone ....

所以可以推斷：當OOM發生時，該service 被 LowMemoryKiller 殺了, restart -> 重新註冊receiver, ->收到intent
然後就一直跳一直跳一直跳  我是說Dialog.

解決的辦法很簡單，插旗子確認intent有收過就好了


