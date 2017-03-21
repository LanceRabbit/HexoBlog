---
layout: post
title: '[Git] Git - fatal: Unable to create '/path/my_project/.git/index.lock': File exists.'
date: 2015-06-29 16:46
comments: true
categories: Version Control
tag:
	- git
---
剛剛準備上code的時候 ommit 到一半 git crash.

重啟後    不管怎樣  `git commit` 都會失敗
錯誤訊息如下：
```
fatal: Unable to create '/path/my_proj/.git/index.lock': File exists.

If no other git process is currently running, this probably means a
git process crashed in this repository earlier. Make sure no other git
process is running and remove the file manually to continue.
```

於是我 剛剛嘗試 `git reset --soft` 或是 `git commit --amend`  都無用.

一氣之下就把 `.git/index.Lock` 移除了.

然後.........






就可以commit 了   =  = " 


以後遇到類似問題  請記得下  ```rm -f ./.git/index.lock```

