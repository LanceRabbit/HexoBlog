
---
title: 一些使用 Docker 的紀錄
date: 2017-03-28 23:26:54
categories: Docker
tags: 
	- docker import
	- docker run
	- docker load
----

# Docker  

[Docker Wiki](https://zh.wikipedia.org/wiki/Docker_(%E8%BB%9F%E9%AB%94))


### Docker import :  
docker-import - Create an empty filesystem image and import the contents of the tarball (.tar, .tar.gz, .tgz, .bzip, .tar.xz, .txz) into it, then optionally tag it.  
 `docker import [-c|--change[=[]]] [-m|--message[=MESSAGE]] [--help] file|URL|-[REPOSITORY[:TAG]]`  
 範例： `docker import http://127.0.0.1/ubuntu1604_.tar.gz ubuntu1604_i686_env`   
 從遠端匯入一個容器(Container) 放到本地的repository  

### Docker load :  
docker-load - Load an image from a tar archive or STDIN.  
`docker load [--help] [-i|--input[=INPUT]] [-q|--quiet]`  
範例：`sudo docker load --input ubuntu_14.04.tar`  
`--input = Read from a tar archive file, instead of STDIN. The tarball may be compressed with gzip, bzip, or xz`  
從本地端(local)載入image  

### Docker run :  
docker-run - Run a command in a new container.
有點多 ... 先放上公司常用的command. 附上一些參數是什麼意思
`docker run --privileged --rm -it -e DISPLAY=unix$DISPLAY -v /dev/video0:/dev/video0`

* -i : attach後，鍵盤輸入會導到Container  
* -t : attach後，Container的螢幕輸入會導致目前電腦的螢幕  
* -it: 就是... -i + -t  
* -e : 設定環境的變數 ex: 設定DISPLAY的參數  
* -d : detached, container 會在背景執行  
* -v : 把container的volume 接到 本機電腦 -v [Host volume]:[Container volume]  
* --rm: Docker結束後會自動移除容器 (ㄜ..不太會翻譯)  
* --name : 執行這個名字的容器  

### Docker load 跟 import 差異
import 結束後不會紀錄剛剛在container上的操作，下次在run時還是回到一樣的初始狀態  
load 會持續紀錄在container上的操作 -> 檔案會越來越肥 (如果一直加檔案)

# 後續持續更新