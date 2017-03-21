---
layout: post
title: '[Android] Install genymotion '
date: 2015-11-17 14:17
comments: true
categories: Android
tag:
	- Android studio
	- Genymotion
	- Emulator
---
[Genymotion](https://www.genymotion.com/#!/) 是一套強大的android emulator.  
不管是自己在電腦上玩，或是用為開發App都是相當快速且便利的

魯小我是為了沒事寫個App可以玩，想說安裝一下。而且感覺很好玩 xD

這邊分享的是Linux安裝的過程 (因為實在太不順了)

事先準備工作：
1. 請先確認電腦的CPU為何！這個非常重要. -> 重開機 -> 進BIOS
    **Intel**  CPU, 請到 Advanced -> CPU Configuration -> enable Intel Virtualization Technology
    **AMD**CPU, 請到 Advanced -> CPU Configuration -> enable AMD Secure Virtual Machine or SVM Mode
    Reason: 因為 Genymotion emulator 啟動時需要使用 virtual box. 必須enable 上述的功能.
    如果BIOS找不到這兩項, 那麼你可以...... 換一台電腦 (我也沒招)

2. 請安裝 [Virtual box](https://www.virtualbox.org/wiki/Downloads) (如果已經安裝virtual box.  請跳過這個步驟)
    安裝完 Virtual box 後請記得裝****VirtualBox 5.0.10 Oracle VM VirtualBox Extension Pack****
    
3. 都安裝好後，請先run `sudo /etc/init.d/vboxdrv status`
    如果都設定好了，應該會出現以下的message ```
    You should get the message VirtualBox kernel modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci) are loaded
    ```
    如果不是...  印象中應該出現 not load or not ready之類的訊息. 
    此時請在 terminal 下 `sudo /etc/init.d/vboxdrv setup`
4. 請修改你的Virtual box 使用者群組： `sudo usermod -a -G vboxusers <login>` 
5. 請到 [Genymotion](https://www.genymotion.com/#!/) 網站下載 Genymotion. 
    如果是公司行號也可以直接購買；如果跟阿魯一樣，請選擇Free ~
6. `./genymotion-2.5.2_x64_debian`  直接安裝，或是可以選擇目錄
7. 安裝好後，請直接執行  `./<genymotion path>/genymotion`  確定可以執行.

基本上Genymotion 安裝步驟如上, 那前言提到的很不順...
首先是Virtual box 的安裝, 因為之前亂裝了一些套件跟Lubuntu, 導致很多virtual box 的元件depend on lubuntu,
然後就.....   ``` sudo apt-get remove virtualbox-dkms```
然後在重新安裝 virtual box ...

後來遇到這個問題  ```Starting VirtualBox kernel modules ...failed!
  (modprobe vboxdrv failed. Please use 'dmesg' to find out why)``` , Google了一下....
  gcc 版本不對!!!!  (```gcc -v```) , Ubuntu 請裝gcc 4.8
  
  
virtual box裝好了.  開不起來, 原因就是第一步.  BIOS的設定沒弄好. Orz ...

大致上就這些不順 

Genymotion裝好了, 當然會想把他套用在Android studio上. 
    - Launch  android studio
    - File -> Settings -> Plugin -> Browse repostories -> Search "genymotion"
    - Install 
    - Restart android studio





Q&A
**Q1:**  *Android sutdio 不是就有emulator了嘛？為什麼要用Genymotion*
A  :  Android studio 的emulator 開啟時相當耗費時間. 使用genymotion相對減少開啟時間

**Q2: enymotion 缺點？**
A :  目前 genymotion 只有 手機跟平板的emulator, 原生的有wearable的emulator 

Q3：有想到再補充


