---
layout: post
title: Setup environment for build Android open source project
date: 2014-09-26 02:44:01
categories:
	- Linux
tag: 
	- Android open source project
---
因為工作需求，有一台Linux電腦專門在build android codebase 是很正常的事情:

## 你需要一台電腦
## 請先下載ubuntu iso [ubuntu官網](http://www.ubuntu.com/download/desktop)
安裝ubuntu. (燒錄成光碟或是使用usb安裝)
## 先連接上網路
## 安裝JDK. (Java develope kit). 
####"NOTE: 不要安裝open-jdk, 請安裝 jdk6" for Android 5.0 lower
1. 請到oracle 下載 [Java SE 6. ](http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-javase6-419409.html)
2. 安裝jdk-6
    <pre>
    $ sudo chmod 755 ./jdk-6u32-linux-x64.bin
    $ sudo -s ./jdk-6u32-linux-x64.bin /opt  // 一直next.
    </pre>
3. 修改class-path
    <pre>
    $ sudo gedit .bashrc
    在檔案最後貼上 
        export JAVA_HOME="/opt/java/jdk"
        export JRE_HOME="$JAVA_HOME/jre"
        export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
        export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
    存檔  離開
    </pre>
4. 先確認 /usr/lib/jvm 路徑是否存在，若否: 搬移jdk資料夾
    <pre>
    $ sudo mkdir /usr/lib/jvm
    $ sudo mv jdk1.6.0_32 /usr/lib/jvm
    </pre>  
5. 搬好資料夾後
    <pre>
    $ sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.6.0_32/bin/java" 1
    $ sudo update-alternatives --install "/usr/lib/mozilla/plugins/libjavaplugin.so" "mozilla-javaplugin.so" "/usr/lib/jvm/jdk1.6.0_32/jre/lib/amd64/libnpjp2.so" 1
    </pre>
6. 切換Java的設定
    <pre>
    $ sudo update-alternatives --config java
    $ sudo update-alternatives --config mozilla-javaplugin.so
    </pre>
7. 查看目前Java版本
    <pre>
    $java -version
    </pre>

## Install packages
<pre>
$ sudo apt-get install git gnupg flex bison gperf build-essential zip curl libc6-dev libncurses5-dev:i386 x11proto-core-dev libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-glx:i386 libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev:i386
  $ sudo ln -s /usr/lib/i386-linux-gnu/mesa/libGL.so.1 /usr/lib/i386-linux-gnu/libGL.so
  $ sudo apt-get install software-properties-common
  $ sudo apt-get install python-software-properties
</pre>

## 安裝Openjdk-7-jdk

<pre bash>
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-7-jdk
</pre>