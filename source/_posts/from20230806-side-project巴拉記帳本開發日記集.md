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
  - ResTool傳入值跟適用情境不夠，之後要改
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
  - [Django不小心新增一個空字串作為PK 我無法使用管理後台刪除該筆資料](https://chat.openai.com/share/f8939787-a942-4e02-a4d1-eeccb4ce9507)
  
##### 序列化器出來的東西跟我想的不一樣
~~DRF初學者寫Join要命rrr~~

明天來做點釐清...



