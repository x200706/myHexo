---
title: 20230802 Hexo Shoka主題安裝、留言及搜尋功能配置紀實
date: 2023-08-02T13:03:00.000+08:00
updated: 
tag: 
  - Hexo
  - Shoka
categories: 
  - [Hexo]
cover: 
---
### 自定義頁面
這是Hexo級別的問題，可參考
要改樣版可參考：[Hexo 入门指南（四） - 页面、导航、边栏、底栏](https://developer.aliyun.com/article/595844)

### 圖片換不了
預設的新浪圖床壞掉了，所以得抽換為自己的圖庫

如果你按照作者說法，在source/_data新增了images.yml，上面寫了六張圖片...但網站運行還是破圖！！根據我爬文作者文章下方的留言問答，是作者筆誤，要放7張圖才能覆蓋喔XDrz

另外如果有外連圖床，就可以不用寫images.yml，直接在_config.shoka.yml加入：
```yml
image_server: "https://source.unsplash.com/1600x900/?snow"
```
可以用[Unsplash的API](https://unsplash.com/documentation#search-photos)，懶得申請帳號也能用public的API，Ithome上有不少[相關文章](https://ithelp.ithome.com.tw/articles/10247379)，可以去查詢看看

### 抽換圖片
>透過yml抽換Avatar頭圖，作者說得蠻詳細，在此跳過


在source/_data下新增目錄images，把跟原作者主題儲藏庫下方source/images同名同副檔名的圖片加入到這個新建的目錄，它就會蓋掉預設，以下是我改過的部分

|檔名|功能|
|---|---|
|search.png|搜尋頁面右下角的插圖|
|favicon.ico|網站小圖示|

大小盡量不要跟原始素材差太多，不然可能會顯示異常

### 抽換瀏覽器分頁轉換時的標題字樣
source/_data/新增一個languages.yml，以你Hexo config.yml設置的語系（Shoka有提供的語系）出發去覆蓋
```yml
zh-TW:
  favicon:
    show: 開心你來到我的網站(つ´ω`)つ
    hide: 快看看我🥺
```

### 改按鈕顏色
到原作者主題下方的source/css/_color.styl參考既有寫法，回到你自己hexo的source/_data，新增color.styl，把想改的樣式區段複製下來

要注意的是styl的註解跟CSS一樣是`//`，我就曾誤寫成`#`網站渲染壞掉XDrz|||

### Static CMS後台Field設置
這原始後台Netlify CMS(其實是Decap CMS)跟這個抽換的Static CMS後台設置的yml很相似（似乎因為Static CMS是基於前者Decap CMS的前身Netlify CMS ~~這幾個專案祖譜有點複雜~~）

首先我們要知道一般Hexo Post的Front-matter有哪些欄位，可以參考[這篇文章](https://zhuanlan.zhihu.com/p/645394041)

另外Shoka主題提供了`sticky: true`來置頂文章，但這邊有個坑—

這樣寫會網站壞掉：
```yaml
---
sticky: false
title: TEST
date: 2023-08-01T12:31:19.281+08:00
updated: 2023-08-01T12:31:20.646+08:00
tag: 
  - thisSite
categories: 
  - thisSite
cover: https://i.imgur.com/8ZlAuRL.png
---
```

這樣寫不會壞掉：
```yaml
---
title: TEST
date: 2023-08-01T12:31:19.281+08:00
updated: 2023-08-01T12:31:20.646+08:00
tag: 
  - thisSite
categories: 
  - thisSite
cover: https://i.imgur.com/8ZlAuRL.png
---
```

交叉試了很久，直接說結論
1. 全站不能只有一篇文章時還設置頂
2. 置頂不能填false


所以我不建議在後台加入Sticky的Boolean Filed

接著我們要了解Hexo分類跟標籤的特性—可以帶yml list，但同樣寫法意義不同
```yml
tag: 
  - a
  - b # 這篇文章有a跟b的標籤
# 在CMS的寫法：a, b
#-------------我是分隔線----------------#
categories:
  - a
  - b # 這篇文章在a下面的b子分類
# 等校於
categories:
  - [a,b] # 這篇文章在a下面的b子分類
# 在CMS的寫法：a, b

# 一個文章屬於兩個分類的寫法
categories:
  - [a]
  - [b] 
# 在CMS的寫法：[a], [b]
# 這邊有個坑，Static CMS list filed是以逗號區隔，所以當你希望文章有[a,b]又有[c]兩種分類時，如果寫[a, b], [c]會產生不符合預期的格式，目前考慮文章應該暫時不會有這麼複雜的分類結構，我沒有花時間下去繼續研究，突然想到，不知道寫成a, b, [c]會是什麼效果🤔...但暫時不想花太多時間在這裡了
```
以上結論參考文章：[1](https://guiblogs.com/hexo30-19/) [2](https://theriseofdavid.github.io/2020/07/20/blog/blog-categories/) [3](https://www.zhihu.com/question/48934747)
接下來就能設計你的Filed了，可以參考這個[範例](https://github.com/jiangtj/hexo-netlify-cms/blob/master/admin/config.yml)，輸入時標籤跟分類時要記住上方代碼塊提及的特性

其他Static CMS的Filed可以看[官方文件說明](https://www.zhihu.com/question/48934747)，歷史遺跡：[Netlify CMS的Filed](https://preview-auth-doc--netlify-cms-www.netlify.app/docs/widgets/)

還可以修改文章生成的目錄 [1](https://zhuanlan.zhihu.com/p/73784650)，我的網站目前沒有改生成目錄是因為我覺得分類跟標籤都是可變的..

***
### 結語
應該大部分基本設置跟簡易美化到這邊都結束了，但這個主題的markdown渲染器還有很多奧義，可以參考以下幾篇文章：
- [官方指南](https://shoka.lostyu.me/computer-science/note/theme-shoka-doc/special/)
- [網友的測試](https://linn-ylz.com/Hexo/blog-content-test/)

還有些[置頂文章的配置](http://8.134.208.248/2021/11/11/application/hexo/shoka%E4%B8%BB%E9%A2%98%E6%A1%88%E4%BE%8B-step3.%E7%95%8C%E9%9D%A2%E8%AE%BE%E8%AE%A1/)（其實官方也有寫到）

想要fork下來暴改的，像是換掉loading貓條，可以參考[這個站](https://www.lavenderdh.cn/)