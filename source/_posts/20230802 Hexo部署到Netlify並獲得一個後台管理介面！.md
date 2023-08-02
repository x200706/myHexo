---
title: 20230802 Hexo部署到Netlify並獲得一個後台管理介面！
date: 2023-08-02T13:01:00.000+08:00
updated: 
tag: 
  - Hexo
  - Shoka
categories: 
  - [Hexo]
cover: 
---
### 前言：名詞定義
|名詞|意思|
|---|---|
|Decap CMS|Netlify CMS後來停止開發了，這是接手下來的專案，也就是在[這個庫](https://github.com/DemoMacro/hexo-boilerplate-netlify-cms)管理後台首頁中間插入的cdn網址，所看到的Netlify CMS2.0.0|
|Netlify CMS|Netlify最初提供的靜態網頁後台管理工具|

***
以下按照步驟下去敘述，內含踩過的坑Orz...

### Deploy
有時候我蠻懶的，所以我是之前逛GitHub時，去找這類型現成的儲藏庫，想說fork之類的就能得到一個Hexo（...），Starred了好幾個，最後實作我選了[這個庫](https://github.com/DemoMacro/hexo-boilerplate-netlify-cms)，因為它會幫我自動部署到可以為靜態網頁安裝後台管理界面的Netlify[1]
[1]: 免費方案免費300分鐘Build時數，我自己用下來，Hexo發單篇文章或單純調樣式每次Deploy大概花幾秒，如果透過本機一次要發很多篇文章推版，會花稍為久一點的時間

進到這個儲藏庫，看到README，點那個藍色上面有寫Deploy字樣的按鈕就對了xd，它會把你帶到Netlify，請你創建一個新的GitHub儲藏庫去放你的Hexo跟管理後台相關檔案，至於部署方案跟語法，前者Netlify會自動偵測為Hexo，後者是這個庫作者已經幫我們寫好`netlify.toml`。
>不過之後另篇主題設置的文章會提到，如果使用Algolia搜尋工具，要修改部署語法。

### 初次部署失敗
原因是Netlify預設的啟動方式不適用本專案，請參考[這篇討論的第一個回答修改CI啟動命令跟環境設置](https://stackoverflow.com/questions/64468843/netlify-deployment-failed-during-stage-building-site-build-script-returned-n)

### 讓Netlify CMS(其實是Decap CMS)後台能夠登入
但剛剛那個庫從==Add the Netlify Identity Widget==開始其實是我們要開始手作的

一開始我以為這個庫已經處理好我該改的部分了，但在網站網址後加入/admin進到後台，輸入帳密並沒辦法登入，後來根據[這篇討論](https://answers.netlify.com/t/common-issue-netlify-cms-git-gateway-email-not-confirmed/10690)得知：
1. 電子郵件預設範本給你的網址上是有token，但少在你的網站後方描了/admim，但做到這步即使寫了完整網址也沒用，下方會說原因
1. 配置成功的畫面應該是會出現一個驗證按鈕而非登入輸入框
2. 初次登入應該要是能設定後台密碼的

點1肇因於source/admin/index.html的head內缺少了Netlify身分驗證工具，我們得手動加上它：
```html
<script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
```
其實加在後台頁面首頁就夠了，有些文章會要你想辦法加在網站根目錄（這樣信箱的驗證連結應該就能作用了），但Hexo的首頁是渲染出來的，要加這段代碼是有點麻煩

但這樣還是不夠的，如果你是按照本文步驟建置，由於用於deploy的庫年份稍早，這中間GitHub已經把master分支統一改為main，所以即使你身分驗證方面的問題處理完，這時候你想登入還是會[一直跳出git gateway相關的錯誤](https://answers.netlify.com/t/git-gateway-error-please-ask-your-site-administrator-to-reissue-the-git-gateway-token/30039/8)，所以你要到source/admin/config.yml修改你的分支為main
```yml
backend:
  name: git-gateway
  branch: main # 改成main
```

其他參考文章：
- [Netlify身分驗證工具官方文件](https://docs.netlify.com/visitor-access/identity/)
- [Decap CMS官方教你手把手搭建後台](https://decapcms.org/docs/add-to-your-site/#add-the-netlify-identity-widget)
- [記得開F12檢查頁面有沒有確實加上身分驗證小工具](https://answers.netlify.com/t/missing-netlify-identity-modal-on-password-reset/33107/26)
- [缺少身分驗證工具基本上無法使用](https://medium.com/netlify/adding-netlify-cms-and-redirects-to-hexo-site-the-missing-pieces-c69a8ec053d1)
- [如果早點看到這篇或許能少花點時間-.-](https://www.ituring.com.cn/article/507586)
- [這篇也是](https://hexo.fluid-dev.com/posts/hexo-netlify/)

### 怒換後台為Static CMS
然而好不容易讓後台可以登入使用了，我發現中文輸入游標會亂跑，完全無法打字，後來發現是現行Decap CMS一個長期未修的bug，其源頭是Slate JS某版出現了Bug（[像Grafana有陣子也因此出現這個bug](https://github.com/grafana/grafana/issues/54942)），根據[討論](https://github.com/decaporg/decap-cms/issues/6287)加了CSS樣是強蓋也毫無作用，這樣怎麼用啦= =...

很苦惱哇...後來看到[這篇文章](https://vrabe.tw/blog/continuation-of-netlify-cms)提到可以抽換為Static CMS，就能進行中文輸入了

以下是抽換完的source/admin/index.html
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="https://unpkg.com/@staticcms/app@^2.0.0/dist/main.css" />
    <script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
    <title>Content Manager</title>
  </head>
  <body>
    <script src="https://unpkg.com/@staticcms/app@^2.0.0/dist/static-cms-app.js"></script>
    <script>
      window.CMS.init();
    </script>
  </body>
</html>
```
不過這個Static CMS不像Netlify CMS(其實是Decap CMS)可以從富文本編輯器切換成原始碼編輯器，微可惜

另外可以在後台的config.yml調整語言界面，這些是[Decap CMS有支援的語言](https://github.com/decaporg/decap-cms/tree/master/packages/netlify-cms-locales/src)，應該跟Static CMS有支援的差不多，需要到專案下方語言包查證。抽換後繁體中文還是能作用的，所以得知至少Static CMS也是支援繁體中文的

source/admin/index.html抽換語言的設置：
```yml
locale: "zh_Hant"
```
至於後台Posts的欄位設置，因為跟我想安裝的主題Shoka有點關係，所以之後其他篇文章再說

***
### 結語
到目前為止，訪問你的https://專案名稱.netlify.app/應該就能看到你的Hexo還有預設的主題了，但這似乎只是開始...因為主題安裝可能也是個大坑;w;，下一篇再繼續解說