---
sticky: true
title: From20230806 Side Project巴拉記帳本開發日記集
date: 2023-08-06T19:49:55.971
tag:
  - Django
  - Vue
  - date
  - Django Admin
  - DRF Serializer
  - Django Model Field
updated: 2023-08-08T19:49:55.979
categories:
  - [Django]
  - [Vue]
---
- [巴拉記帳本的後端源碼](https://github.com/x200706/BlahAccountBookAP)
- [巴拉記帳本的前端源碼](https://github.com/x200706/BlahAccountBook)
- [巴拉記帳本的前台畫面](https://blahaccountbook.chi200706.repl.co/)
  - 因為是用Replit架的，所以太久沒人連線前端或後端API可能會睡著`-.-`，之後完成後這個side project後再考慮弄個程式定期叫醒

### 20230806 Day01
> 其實之前就在寫了，但因為各種事情 ~~還有分心~~暫時停擺了，最近閒賦在家當然要趕快重新開始！！那就以重新開始這天稱為第一天吧！

#### 前端進度
- 減少第一版要寫的功能以增加完成的機率=_=
- 找到一個合作小夥伴（應該）

#### 後端進度
- 開始寫View，暫時維持使用ResTool 完美主義延續下去根本第一版都出不來`~_~`
- 遇到的問題
  - [no such table...記得異動資料庫後要下後續的兩條指令=_=](https://blog.csdn.net/qq_33654685/article/details/88573873)
  - 日期可以傳入20230806欸0.0...
  - ⭐ResTool傳入值跟適用情境不夠，之後要改
  - [關於DRF的後台](https://stackoverflow.com/questions/63652653/does-the-django-rest-framework-provide-an-admin-site-to-manage-models)
    - [Models沒有顯示是因為沒有註冊](https://stackoverflow.com/questions/2984987/model-not-showing-up-in-django-admin)->[註冊自動化](https://hackernoon.com/automatically-register-all-models-in-django-admin-django-tips-481382cf75e5)
    - 統整下來在app下方的`admin.py`加入以下：
    ```python
    from django.contrib import admin
    from django.apps import apps

    models = apps.get_models()

    for model in models:
        try:
            admin.site.register(model)
        except admin.sites.AlreadyRegistered: # 跳過重複註冊的
            pass
    ```
  - 欄位的最小長度要用什麼敘述？ A：預設沒有這個敘述，[要自己擴寫](https://stackoverflow.com/questions/15845116/how-to-set-min-length-for-models-textfield)
  - ⭐[Django不小心新增一個空字串作為PK 我無法使用管理後台刪除該筆資料](https://chat.openai.com/share/f8939787-a942-4e02-a4d1-eeccb4ce9507)
    - 朋友解釋是因為Python的Dict不能以空字串作為key，所以對Python來說**沒有這個東西**，自然也就**刪除不到**
      - 猜測用delete方法的接口效果也一樣（中間也過序列化器，應該也涉及Python資料結構），討論完的結論就是...大大用Chrome打開SQLite刪吧囧
      - 請做好空字串防止！！
  - 序列化器出來的東西跟我想的不一樣 ~~DRF初學者寫Join要命rrr~~->明天來做點釐清...

***
### 20230807 Day02
#### 前端進度
- 暫無
- 組件趕快整理出來才能讓小夥伴幫忙r >_<
#### 後端進度
- 研究DRF序列化器
- 代碼整理
  - 重新設計ResTool
  注意：原生Python[沒有多載](https://www.uj5u.com/houduan/288561.html)，方法簽章相同時，後面method覆蓋前面method
  ```py
  from django.http import JsonResponse
  class ResponseTool():
    def success_json_res(data):
        response_data = {
            "code": "SUCCESS",
            "message": "成功",
            "data": data
        }
        return JsonResponse(response_data, safe=False)
    
    def exception_json_res(Exception):
        response_data = {
            "code": "SOME_EXCEPTION",
            "message": str(Exception),
            "data": {}
        }
        return JsonResponse(response_data, safe=False)
  ```
    - 補充：[save參數的意義](https://dev.to/chryzcode/django-json-response-safe-false-4f9i)

***
### 20230808 Day03
#### 前端進度
- 無
- 確認Axios可以用[put method](https://rapidapi.com/guides/use-axios-put-patch-requests)
#### 後端進度
- 研習QuerySet，還借了書。

***
### 20230809 Day04
#### 前端進度
- 無
#### 後端進度
- 研習kwargs、網址傳參

***
### 20230810 Day05
#### 前端進度
- 無
#### 後端進度
- API撰寫
  - 稍微改良一下views.py主要的寫法

今天遇到的問題，主要發生在查全部記帳不傳入參數：（貌似是昨天就寫出來的問題，但沒測到就收工了）
1. TypeError:缺少必要參數
  >...missing 1 required positional argument: 'id'

  似乎是類似[這樣](https://stackoverflow.com/questions/69173337/i-have-this-error-delete-missing-1-required-positional-argument-id-when-i-c)的問題，後來改寫法後是沒遇到了
2. 查不到東西？？
  >myapp.models.Account.DoesNotExist: Account matching query does not exist.
  
  我想了一下它似乎進到if裡了，但又是空的，所以查不到東西
  
  後來我印一印得知不傳參數，那個參數會是`None`，寫法用是否為空字串判斷當然是❌❌

  改成這樣就修好囉
  ```py
  if kwargs.get('id') != None:
  ```

***
### 20230811 Day06
裸辭...很可怕欸，變成自然醒又懶洋洋的，跟我本來的期望有點落差`= =` 不過就當休息也不錯？只是之前指望自己可以做到很多，其實實際上能做到的很少`T_T`
#### 前端進度
- 無
#### 後端進度
- API代碼規劃與撰寫
  - 修改記帳：`def put(self, request,*args, **kwargs)`
  - 刪除記帳：`def delete(self, request,*args, **kwargs)`
  - 修改分類：基於關聯安全考量加上暫時沒配套措施，我只讓它修改中文desc
  - 刪除分類：暫無，因為有關聯，給使用者單純刪可危險了，需要研擬配套措施
  - 額...之後查詢類的API怎麼取名才顯得RESTful，去取材吧...
- 已知使用[Python的TODO](https://tw-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules.html)

***
### 20230812 Day07
### 20230813 Day08
#### 前端進度
- 無
#### 後端進度
- RESTful API命名考究，閱讀文章[1](https://www.ibm.com/docs/zh-tw/urbancode-build/6.1.3?topic=reference-rest-api-conventions) [2](https://www.gushiciku.cn/pl/grIf/zh-tw) [3](https://www.jianshu.com/p/3374882b19a2)

***
### 20230814 Day09
#### 前端進度
- 無
#### 後端進度
- 圖表接口安排與urlpatterns研究

***
### 20230815 Day10
#### 前端進度
- 無
#### 後端進度
- Queryset與運算
  - [遍歷與效能](https://stackoverflow.com/questions/44066026/should-i-iterate-on-django-query-set-or-over-the-variable)
  - 使用[串列](https://wenyuangg.github.io/posts/python3/python-list.html)快速做數學運算吧
  - [dict存放資料](https://www.digitalocean.com/community/tutorials/python-add-to-dictionary)
- 要做針對帳目的計算時才發現之前根本沒有記錄金錢的欄位（狂汗），給它補上
  - 如果sqlite已經存在多筆資料，在model沒給預設值的狀況，執行`python manage.py makemigrations`，它會問你要(1)現在給那些既有資料一個值；還是(2)在model定義預設值
  - 這個欄位需要做資料防護，必須>=0，因為原先設計views.py程式中是用io欄位的INPUT/OUTPUT去決定最終正負號
- [神秘錯誤一則](https://stackoverflow.com/questions/32121804/django-rest-framework-attributeerror-when-serializer-many-false-but-not-when-m)

***
### 20230816 Day11
#### 前端進度
- 稍微想像了下串接..
- 在本機clone了專案
#### 後端進度
- 完成最後一支API
- 單引雙引的差別複習
- 閱讀[1](https://learnku.com/docs/python-learning/serializer-serializer/10886)

>下次作業日才為Day12