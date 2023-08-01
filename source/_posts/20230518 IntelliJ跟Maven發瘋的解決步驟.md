---
title: 20230518 IntelliJ跟Maven發瘋的解決步驟
date: 2023-08-01T15:00:00.000+08:00
updated: 
tag: 
  - IntelliJ
categories: 
  - IntelliJ
cover: 
---
- 主要是終端機下`mvn package`或`mvn clean`說找不到Maven Plugin的jar檔...？（但可以雙擊使用IntelliJ右方面板上的`clean`或`package`）
  - [熱烈討論串](https://stackoverflow.com/questions/20496239/maven-plugins-can-not-be-found-in-intellij?page=1&tab=scoredesc#tab-top)

上方討論串提供了一些方法：

刪除線是遇到事故時沒用的（幾個同事的電腦），皇冠是有用的（或可能有用的）
1. ~~檢查Proxy設定~~
2. ~~清空REPO目錄下的套件，重新安裝（沒有自動執行可以在`pom.xml`按右鍵，選Maven-Reload）->這個論點是有人認為下載時損毀套件了~~
3. ~~取消核取Setting-Maven中Ｗork offline這個設定~~
4. 👑File > Invalidate caches->同事沒試，但我覺得應該有用？！效果類似重裝IntelliJ
5. ~~直接加提示懸缺之Plugin依賴至pom.xml，下載完再刪掉->但似乎不是很正派~~
6. ~~這是來自另篇討論串（翻瀏覽紀錄找不到了Orz）刪除REPO套件目錄下有remote字樣的一個檔案...？->沒試過~~
7. 👑不要用IntelliJ的Maven，用本機的
8. ~~留意Setting-Maven-Import關於JDK的設置~~
9. 👑重裝IntelliJ=_=
10. ~~重裝Maven並使用之~~
11. ~~檢查網路連線，理由類似第2點~~

不過劃刪除線項目不一定在您的電腦是無效的，都能嘗試看看`;w;`