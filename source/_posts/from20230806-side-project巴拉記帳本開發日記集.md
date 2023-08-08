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
updated: 2023-08-06T19:49:55.979
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
