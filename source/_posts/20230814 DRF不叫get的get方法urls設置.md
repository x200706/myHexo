---
title: 20230814 DRF不叫get的get方法urls設置
date: 2023-08-14T21:16:55.971
tag:
  - Django
updated: 2023-08-14T21:16:55.971
categories:
  - [Django]
---
~~標題賊饒舌~~
##### 今天的issue
要給記帳本的五個圖表各一個get方法，選了最老套JSON-RPC的寫法，但又想用一個類集中管理這些def，但實作上遇到了些問題

調整中的寫法
urls.py
```py
    path('canva/CurrentMonthPie', views.CanvaView.as_view(),name='current_month_pie'),
```
views.py
```py
class CanvaView(GenericAPIView):
    def current_month_pie(self, request):
        accounts = Account.objects.all()
        data = {}
        return ResponseTool.success_json_res(data)
```
遇到的問題
>Method \"GET\" not allowed.

相關文章：
- [detail": "Method \"GET\" not allowed. | Django Rest Framework](https://stackoverflow.com/questions/75179072/detail-method-get-not-allowed-django-rest-framework)
  - 讀下來調整了一下`urls.py`的寫法，但還是噴一樣的錯誤`;w;`，但我注意到它這邊有個裝飾器欸
    - [【零基礎成為 AI 解夢大師秘笈】Day10 - Django VII](https://ithelp.ithome.com.tw/articles/10244040)：照著加了還是一樣的問題
  - 繼續觀察第一篇文章的代碼，改成直接呼叫def呢？
    - [調用學問大...](https://stackoverflow.com/questions/45720065/django-missing-1-required-positional-argument-request) [2](https://stackoverflow.com/questions/71838282/the-request-argument-must-be-an-instance-of-django-http-httprequest-not-bu) [3](https://stackoverflow.com/questions/64220053/how-to-convert-a-django-rest-framework-request-object-with-data-to-a-django-http)

好唄...讀了這麼多篇，總之不同寫法要注意它父類別或裝飾器傳參的細節，才不會出現類似
>TypeError: View.as_view.<locals>.view() missing 1 required positional argument: 'request' #沒加裝飾器會變成缺2，但如果不知道自己在幹嘛就會被誤導==

>AssertionError: The `request` argument must be an instance of `django.http.HttpRequest`, not `myapp.views.CanvaView`.

>TypeError: CanvaView.current_month_pie() takes 1 positional argument but 2 were given

改良後可以打通的寫法
urls.py
```py
    path('canva/CurrentMonthPie', views.CanvaView().current_month_pie), # CanvaView()表這個物件本身，這樣才能正確調用到def，不然它會說它找不到 沒有這個成員
```
views.py
```py
class CanvaView(GenericAPIView):
#後來又把裝飾器拔掉惹ˊ_>ˋ
    def current_month_pie(self, request):
        accounts = Account.objects.all()
        data = {}
        return ResponseTool.success_json_res(data)
```
哇，總覺得這裡有個坑...是該好好研究一下..

得從DRF的基礎了解
- [Django REST Framework学习笔记](https://zhuanlan.zhihu.com/p/44379108)
  - 姆姆姆...好像用[ViewSet](https://blog.csdn.net/weixin_43661701/article/details/111880470)寫更漂亮..而且它本來就是讓人指定傳參的
  - 沒有類的世界，[直接調用def](https://zhuanlan.zhihu.com/p/35348643)，上方stackoverflow也有人提出這種解答
- [Django Rest FrameWork基礎筆記](https://hackmd.io/@_FqBW8dGS8a5ZqhdMwvpuA/H1gZmMGzv)

唉，沒頭沒腦 錯中學