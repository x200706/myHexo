---
title: 20230802 Hexo Shoka主題安裝、留言及搜尋功能配置紀實
date: 2023-08-02T13:02:00.000+08:00
updated: 
tag: 
  - Hexo
  - Shoka
  - Algolia
  - Valine
categories: 
  - [Hexo]
  # - [Algolia]
  # - [Valine]
cover: 
---
### Shoka主題的基本安裝
會選擇Hexo最主要就是覺得主題很炫啊！！衝著Shoka主題來的 ~~Hugo：效能不重要嗎~~

其實在[官方主題導覽](https://hexo.io/themes/index.html)交叉瀏覽時，知道想裝的Shoka似乎已經停止維護了（作者太忙了），本來想裝ShokaX，但因為一些疏忽裝不起來，畫面渲染一直失敗
>我後來裝完Shoka推測，裝不起來ShokaX的原因是照著ShokaX文件安裝時，文件上少寫步驟、我也不清楚Shoka這個主題的安裝背景（畢竟ShokaX是基於Shoka來的），沒有移除Hexo預裝的Markdown渲染器衝突導致

照著[官方](https://shoka.lostyu.me/computer-science/note/theme-shoka-doc/)教學一步一步安裝
>我假設你們安裝跟調整主題時，是把用作部署的hexo儲存庫`git clone`到到本機用VSCode修改，這階段不推薦用vscode.dev，因為很多報錯得透過CLI提供的訊息去解

應該很快在本機終端輸入`hexo s`就能看到美美的主題'w'，但我在中間還是踩了一些坑，在這邊note下來節省大家時間：
- npm這個東西很囉唆＊喂），它會提醒你一些漏洞，只要不是error其實都不影響功能
  - 但是但是，如果覺得這樣就能把所有安裝主題需要的npm指令寫在一個`*.bat`/`*.sh`一次跑完這麼好康...就錯了><，它會卡在提醒漏洞的地方，==沒裝完任何文中提醒要裝的套件，主題都會渲染失敗==，所以還是一條一條老實跑吧`;w;`
- 因為shoka主題在你hexo儲藏庫的theme目錄下，但安裝方式導致它也來自原作者的儲藏庫，你得在你的hexo儲藏庫跟目錄撰寫`.gitmodules`[去說明組件的版控](https://stackoverflow.com/questions/70909779/fatal-no-url-found-for-submodule-path-dist-in-gitmodules)，不然建置時會一直報錯；另外如果要魔改主題（如果原作者提供的覆寫方案你覺得不足，像是想抽換載入貓條動畫等），你在你hexo/theme下可以更換為你fork原作者Shoka主題到自己帳號下的儲藏庫，也要去改`.gitmodules`內描的網址

***
### Shoka主題的功能配置
剛剛只是安裝完主題的皮，我們又跟著原作者手把手填寫好了_config.yml跟建立_config.shoka.yml並填寫了相關設置，再次進行了部署，但其實仔細一用網站會發現留言跟搜尋功能都是不能用的

### 留言功能配置&解套件衝突
[這教學寫的不錯，如果有早點看到就能少踩坑了;w;](https://gardencavy.site/course/blog/blog01/)

基本上照著[套件官方文件](https://valine.js.org/quickstart.html)申請LeanCloud帳號（台灣要用國際版才能信箱認證），建立APP，把API ID跟Key貼上應該就要能作用了，但為什麼不行呢—請確認以下：

#### 一、LeanCloud國際版在_config.shoka.yml留言板的部分加上serverURL比較穩定
[相關討論](https://github.com/xCss/Valine/issues/340)

跟打開f12拋出網路相關錯誤訊息（常見於中國網友）可能是類似的問題，都是要修改serverURL設置

[這篇文章](https://www.lavenderdh.cn/posts/1013593897/)寫的不錯，但對於國際版要不要加serverURL持保留態度

#### 二、解衝突
打開f12拋出類似沒有物件的[錯誤訊息](https://leancloud.cn/docs/error_code.html#hash1443)，通常[便屬於此](https://zhli.me/2020/02/05/valine-error-prob/)，[參考本文改寫你yml留言板設定的部分](https://zhuanlan.zhihu.com/p/149540221)

#### 三、沒有物件
還是一直卡在載入中嗎？發第一篇留言在LeanCloud產生Comment物件（不喜歡留言再去DB刪掉），重新整理世界就恢復和平了ˇWˇ...
>更正：其實照著上方[推薦的文章](https://gardencavy.site/course/blog/blog01/)先在DB建立Comment物件再開始使用留言板即可

### 搜尋功能配置
[這教學寫的不錯](https://shoka.vercel.app/Algolia.html)，還有教你怎麼建立權限更高的API+在本機初始化索引，[這篇也不錯](https://brooke2010.github.io/2020/03/04/hexo-search-use-Algolia/)

配置完以後，我們每次得在`hexo g`之後跑`hexo algolia`[產生索引](https://israynotarray.com/hexo/20191225/2266233686/)（順序很重要喔，寫反會無效XDD！！）

但之前的Hexo源頭儲藏庫，`netlify.toml`寫的指令為：
```yml
[build]
  publish = "public"
  command = "hexo generate"
```
改為：
```yml
[build]
  publish = "public"
  command = "hexo generate;hexo algolia"
```
才能在生成靜態頁面後接續產生索引

參考：[Linux 執行多個指令](https://www.ltsplus.com/linux/linux-run-multiple-commands)
>重點：&&是上段指令成功後才執行，分號則是不管

#### 替代方案：爬蟲服務？
[Algolia for Netlify](https://www.algolia.com/doc/tools/crawler/netlify-plugin/quick-start/)，會自動安裝變數跟工具到你的Netlify專案上，每次部屬就會觸發爬蟲，但我覺得跟在專案內用`hexo algolia`建立索引速度差太多，而且又要改yml設定~~懶~~，還是沒有採納

***
### 結語
到這邊的時候，你的網站應該可以留言跟搜尋了'w'