---
layout: post
title: '[Android] Camera Intent'
date: 2015-06-29 20:22
comments: true
categories: Android
tag:
	- Intent
	- Camera
---
launch camera:

*INTENT_ACTION_STILL_IMAGE_CAMERA_SECURE* 可以在有 SECURE LOCK的情況下Launch Camera
不過按 back key 就會在回到 LockScreen

```java

private final Intent SECURE_CAMERA_INTENT =	
            new Intent(MediaStore.INTENT_ACTION_STILL_IMAGE_CAMERA_SECURE)	
                    .addFlags(Intent.FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS);	
private final Intent INSECURE_CAMERA_INTENT =	
            new Intent(MediaStore.INTENT_ACTION_STILL_IMAGE_CAMERA);


private Intent getCameraIntent() {	
        LockPatternUtils mLockPatternUtils = new LockPatternUtils(mContext);	
        return mLockPatternUtils.isSecure()	
                ? SECURE_CAMERA_INTENT : INSECURE_CAMERA_INTENT;	
	    }	    }
```

