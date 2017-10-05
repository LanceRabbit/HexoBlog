---
title: 使用 Hexo + github 建立Blog
date: 2017-03-28 23:37:58
categories: Blog
tags:
	- Hexo
	- github
	- markdown
---
這個時候一定要罵一下Logdown, 我真的不相信會隨意放生自己產生的一個平台的人當講師會多有幫助  
  
  
## 設定Github  
1. 請先到你的github建立一個repository: 命名規則是`<your-account.github.io>`  
2. `mkdir <Your blog folder> && cd <Your blog folder>` 可以隨意取無所謂   
3. git clone 這個repository 
4. 請記得設定 userName 跟 Email
  ```
  $ git config --global user.name "username"
  $ git config --global user.email "email address"  
  ```
5. 還有請記得設定 ssh-key:  
  ```
  $ ssh-keygen -t rsa -C "email address"
  ```
  
## 設定 Hexo
1. 安裝 nodeJS  
  - MacOS :  
  ```
  $ brew update  
  $ brew install node #應該會連同 npm 都安裝完成
  ```
  - Ubuntu :
  ```bash
  $ sudo apt update
  $ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -  
  $ sudo apt-get install -y nodejs  
  ```
    or  
    ```bash
    $ sudo apt update
    $ curl -sL https://deb.nodesource.com/setup_7.x |   sudo -E bash -  
    $ sudo apt-get install -y nodejs
  ```
  - 確認版本
  ```
  $ node -v          # 確認 node 版本
  $ npm -veriosn # 確認 npm 版本
  ```
  - 參考資料
  [Ubuntu install NodeJS](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)  
  [MacOS install NodeJS](https://nodejs.org/en/download/package-manager/#osx)
2. 安裝 Hexo  
```
$ npm install -g hexo-cli     # -g 全域安裝
```
3. 進到剛剛 *git clone* 的資料夾:  
```
$ hexo init
$ npm install  
```
4. 修改參數：  
  此時 Blog 的環境設定幾乎都在 `_config.yml`, 請記得修改該設定檔案.
  ```bash _config.yml
 # Site
 title: <Your blog Name>
 subtitle:
 description:
 author: <Your real name or nickname>
 language: zh-tw
 timezone:

 # URL
 ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
  url: https://<your name>.github.io
  root: /
  permalink: :year/:month/:day/:title/
  permalink_defaults:
 # Deployment 
 ## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git          # <-- 請修改為 git
  repo: https://github.com/<username>/<username>.github.io.git  
  branch: master  #  <-- 我沒有修改其他的branch 
  ```
  其實還有很多設定，以後在慢慢寫  
  
此時應該已經算是完成，現在嘗試一下發表文章看看  
  
## 撰寫文章與發佈

* 使用 `hexo new <title>` 新增一篇文章！大概會長這樣:
 ```markdown blog.md
 title: <title>  
 date: 2017-03-28 23:26:54
 tags:
 ```
 (請別忘記這是 markdown語法)
 認真寫好你的blog後：
 ```bash
 $ hexo clean
 $ hexo generate   # mac 上可以直接輸入 hexo g
 $ hexo server       # mac 上可以直接輸入 hexo s, 請在http://localhost:4000預覽blog
 ```
 預覽後如果不滿意就趕緊修 xDDD. 存檔後重新刷新瀏覽器介面就可以確認剛剛修改是否有誤.  
* 將文章佈屬到 github上： `hexo deploy`
 如果有問題無法佈署，請安裝套件：  
 `npm install hexo-deployer-git --save`  
 成功發佈後，應該在 `https://<your name>.github.io/`可以看到剛剛編輯的文章
 
 ## 如果你想換台電腦寫部落格
 我是參照以下步驟做的，個人覺得應該可以work。
 1. 請在 gitbub 另外開一個 repository, 並在舊電腦上 commit 所有得文章與環境設置. (如果theme底下有 .git目錄，請用`rm -rm .git`, 確保theme會被加到新的git)  
 2. push 文章與環境到新的 repository
 3. 在新電腦中，請一樣安裝 git 與 所提到的環境
 4. 在新電腦中安裝 nodeJS
 5. 請不要執行 `hexo init`
 5. git clone 網誌的 repository (Ex: account.github.io)
 
 這樣應該就可以動了