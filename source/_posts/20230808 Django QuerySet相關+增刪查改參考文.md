---
title: 20230808 Django QuerySet相關+增刪查改參考文
date: 2023-08-08T08:16:55.971
tag:
  - Django
  - QuerySet
updated: 2023-08-08T08:16:55.971
categories:
  - [Django]
---
### 閱讀文章
- [Django后端rest最简洁最快最全入门指南，1天学会DRF后端不夸张，看这篇就够了！！](https://zhuanlan.zhihu.com/p/277465995)
- [Django 學習紀錄 8.模型與資料庫](https://ithelp.ithome.com.tw/articles/10212427):這篇很讚呢，教了這個東西
```shell
python manage.py shell
```
感覺驗證查詢結果很方便！！
  - 無法照著文內正常運作的原因：[Class名稱重複？](https://blog.csdn.net/qq_41638872/article/details/130653841)[抽象？](https://stackoverflow.com/questions/35543695/type-object-x-has-no-attribute-objects)[多下一行試試？](https://www.reddit.com/r/django/comments/v2bhsg/type_object_user_has_no_attribute_objects/)

  我後來的下法：
  ```python
  # 留意直譯器模式下引用是否充足！
  from myapp import models
  from myapp.models import Account
  # objects = models.Account()其實不用這行也可以
  test = Account.objects.all()
  test
  ```
  輸出：
  ```py
  <QuerySet [<Account: Account object (1)>, <Account: Account object (2)>, <Account: Account object (3)>, <Account: Account object (4)>, <Account: Account object (5)>, <Account: Account object (6)>, <Account: Account object (7)>]>
  ```
- [DRF框架(四)——单整体改(put)、单局部改(patch)、群局部改(patch)--ListSerializer](https://www.cnblogs.com/wangcuican/p/11695273.html)
  - 之後寫修改記帳感覺可以參考

***
### 直譯器的摸索結果
```python
# test = Account.objects.all()
entity1 = test[0]
entity1.id # output: 1

# 開啟另個查詢吧 練習單筆查詢
qs = Account.objects.get(id=5)
qs # output: <Account: Account object (5)>也就是說這樣可以查詢到id為5的那個物件～

# 練習過濾查詢（可以理解成where）
qs2 = Account.objects.filter(kind='food')
qs2 # output: <QuerySet [<Account: Account object (3)>, <Account: Account object (4)>, <Account: Account object (5)>, <Account: Account object (6)>, <Account: Account object (7)>]>
```
- 還有一個`order_by()`的方法
- 查詢結果還可以一直鏈式寫下去
  - 不是QuerySet的型態就無法再查下去了
  - Spring QueryDSL的鏈式查詢比較像SQL語句，Django ORM比較像物件關係...？
- 關聯查詢
```py
from myapp.models import ItemKinds
i = ItemKinds.account_set # 前面下反一直跳type object 'Account' has no attribute 'itemkinds_set'，總之要注意自己models設計的關聯關係
i # output: <django.db.models.fields.related_descriptors.ReverseManyToOneDescriptor at 0x1dde2dac050>

# 查出類型是食物的記帳：
ik = ItemKinds.objects.get(kind='food')
ik.account_set
ik.account_set.all() # output: <QuerySet [<Account: Account object (3)>, <Account: Account object (4)>, <Account: Account object (5)>, <Account: Account object (6)>, <Account: Account object (7)>]>
```