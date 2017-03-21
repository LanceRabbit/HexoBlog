---
layout: post
title: '[Android] Broadcast receiver'
date: 2015-01-23 15:55
categories: Android
tags:
	- Broadcast receiver
	- Intent
	- 
---
Broadcast receiver: static and non-static.

# Static boradcast recever:

請先註冊一個 "receiver" 在 AndroidManifests.xml
```xml
<receiver
    android:name=".TypeBReceiver">
    <intent-filter>
         <action android:name="test_TypeB" />
    </intent-filter>
</receiver>
```
***android:name***  此為receiver的名稱， 需要額外新增一個檔案名稱與他相同.
***intent-filter*** 此為篩選要接受哪些action的篩選器.

註冊好後   請新增一支java 檔案,
```java

/**
 * Created by alumincan_leu on 2015/1/22.
 */
public class TypeBReceiver extends BroadcastReceiver {
    private final String TAG = "TypeBReceiver";
    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d(TAG, "get type B");
    }
}

```

此種方式，不需要launch activity, 也可以收到broadcast intent. 


# non-static boradcast recever:

此種方法要在Activity 或是 Service 中另外註冊
若是activity or service 沒有被launch, 則不會收到intent.

首先請先import package.

```java
import android.content.IntentFilter;
import android.content.BroadcastReceiver;
import android.content.Intent;
```

宣告一個Broadcast receiver:

```java
private final BroadcastReceiver mReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            Log.d(TAG, "get typeA");
            mTextVire_msg1.setText("");
            if (action.equals(INTENT_ACTION_TYPEA)){
                mTextVire_msg1.setText("getMsg");
            }
        }
    };
```

宣告filter, 要接受哪些 action； 然後註冊broadcast receiver
```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ...................;
        ...................;
        IntentFilter mfilter = new IntentFilter();
        mfilter.addAction(INTENT_ACTION_TYPEA);
        registerReceiver(mReceiver, mfilter);
{
       
```

有註冊  當然要有註銷

```java
@Override
protected void onDestroy(){
    Log.d(TAG, "onDestroy");
    unregisterReceiver(mReceiver);
    super.onDestroy();
}
```

請注意：
如果是在 onCreate 註冊，請在onDestroy註銷; 同理
若是在 onResume 註冊，則請在 onPause 註銷. 
請參閱 android life cycle: [android developer](http://developer.android.com/training/basics/activity-lifecycle/starting.html)
