---
title: 一條龍建立Private NPM by Verdaccio
date: 2021-02-25 12:12:57
categories: 軟體開發
tags: NPM
---

## 本文章可以幫你
1. 安裝與設定verdaccio
2. 將verdaccio掛載到IIS
3. 建立元件並發布到verdaccio
4. 安裝元件並使用
5. 把元件掛載到CI/CD Flow上
6. 一些troubleshooting

## 安裝與設定verdaccio
**安裝十分簡單**
1.安裝verdaccio至globle
``` batch
$ npm install verdaccio -g
```
2.啟動verdaccio
``` batch
$ verdaccio
```
於瀏覽器網址列打下 http://localhost:4873/ 即可見到以下畫面
![](capture01.png)

P.S 如果上傳的package size過大，會出現以下錯誤訊息
![](capture09.png)
可於
C:\Users\{username}\AppData\Roaming\verdaccio\config.yaml
加上**max_body_size: 100mb**
C:\Users\{username}\.npmrc
加上**max_body_size=100mb**

## 將verdaccio掛載到IIS

local測試完成後，試著將verdaccio掛成一個服務吧!!
因為筆者公司的server為windows base，因此試著將他掛到IIS上
官網有十分詳細[教學](https://verdaccio.org/docs/en/iss-server)

1.安裝iisnode
2.於安裝目錄執行
``` batch
$ npm install
```
3.將網站掛到IIS上
![](capture02.png)
4.因需要讓verdaccio去讀寫網站資料夾，因次需要增加角色給予full control
![](capture03.png)
5.增加Hanlder Mappings for iisnode並設定Feature Permission
![](capture04.png)
![](capture05.png)
完成後使用瀏覽器執行出現以下訊息，damnnnn!!!
![](capture06.png)
發現還需要指定nodejs執行的路徑 => [issue](https://stackoverflow.com/questions/50244164/iisnode-module-is-unable-to-start-the-node-exe-process)
![](capture07.png)

完成設置!

## 建立元件並發布到verdaccio

建立元件有幾個地方要注意
1.設置.npmignore，如果使用typescript的話，記得tsconfig.json一定要設定不然會造成路徑衝突
  ![](capture08.png)
2.因筆者建立的Private NPM沒有對外網路，因此選擇將package name加上scope並在.npmrc/.yarnrc設定不同registry
  ![](capture10.png)

  .npmrc
  ``` 
  @leo:registry=http://xx.xx.xx.xx:8089/
  ``` 
  .yarnrc
  ``` 
  "@leo:registry" "http://xx.xx.xx.xx:8089/"
  ``` 
  可以讓npm install或yarn add要安裝scope內的packages時自動使用private NPM
  
## 安裝元件並使用

  1.安裝
  ``` 
   $ npm install @leo/common -s
  ``` 
  2.想要讓程式對應到基礎元件，因此調整了tsconfig.json的baseUrl
    ![](capture11.png)

## 把元件掛載到CI/CD Flow上

  1.建立Build and Publish Job，使用windows batch
  ``` batch
  $ npm publish --registry http://xx.xx.xx.xx:8089/ --always-auth false
  ```
  2.建立版本自動遞增規則
  ![](capture12.png)
  ``` batch
  $ IF NOT "%version%" == "none" (
  $   git reset --hard
  $   npm version %version%
  $   git push "origin" --tags
  $   git push "origin" HEAD:master
  $ ) 
  ```
  3.建立GitLab webhook，版控Merged自動trigger job
  ![](capture13.png)

  如此一來CI/CD Flow也完成，流程如下:
  => branch merge to master 
  => auto trigger jenkins job 
  => build package and publish to private NPM 
  => version update and push tag to origin
  


