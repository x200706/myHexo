---
title: 20230806 Django RESTful Framework豪華MEMO（舊站搬運+合併）
date: 2023-08-06T20:36:12.296
tag:
  - Django
updated: 2023-08-06T20:36:12.297
categories:
  - Django
---
## 最初的專案（20230606）

### 閱讀文章

- [專案建置](https://faizc.notion.site/Django-5d4425d61eba4917aa52067e4b1b2790)
- [用 Django REST Framework 撰寫 RESTful API 並生成 Swagger 文檔（上） — 用Django REST Framework 撰寫 RESTful API](https://zoejoyuliao.medium.com/%E7%94%A8-django-rest-framework-%E6%92%B0%E5%AF%AB-restful-api-%E4%B8%A6%E7%94%9F%E6%88%90-swagger-%E6%96%87%E6%AA%94-7cbef7c8e8d6)

因為想寫成RESTful風格，所以照下面那篇多裝了：

```shell
pip install djangorestframework
```

`setting.py`的INSTALLED_APPS陣列記得加上`'rest_framework'`

---

### 嘗試集+閱讀重點整理

### ⭐加一個專案

```shell
django-admin startproject Project名稱
```

### ⭐加一個app

```shell
Python manage.py startapp app名稱
# 建議不要叫app之類的好像會撞=_=
```

### ⭐起服務

```shell
# 要cd到有manage.py這層
python manage.py runserver
```

### ⭐在SQLite新增一張資料表

`model.py`
```python
from django.db import models
# Create your models here.
class User(models.Model):
    user_name = models.CharField(primary_key=True, max_length=15)
    password = models.CharField(max_length=15)
    name = models.CharField(max_length=20)
```

寫完在cmd後執行以下命令

```shell
python manage.py makemigrations #寫入異動
python manage.py migrate #執行異動
```

SQLite的檢視方式筆記（躺在Trello，待整理）

`serializers.py`

```python
# serializers跟models在同層所以我這樣寫而已
from models import User #我只撿了model裡面的一個小傢伙User
from rest_framework import serializers
class UserSerializer(serializers.ModelSerializer):
     class Meta:
         model = User
         fields = '__all__'
```

`view.py`跟`url`幾乎是拾人（第二篇教學的作者）

牙慧了

#### 問題

> ModuleNotFoundError

照著堆疊追蹤最後一行一直解掉就好，通常是多一層少一層的問題，可以好好研究

例如下方的代碼

```python
from models import User
```

它如果說沒有models

```python
from myapp.models import User
```

就指package給它..

又如下方的代碼

```python
from FirstProject.myapp import views as user_view
```

它如果說沒有FirstProject

```python
from myapp import views as user_view
```

直接同層給它..

---

### 🎉連線時間><

輸入：http://127.0.0.1:8000/users



進到GET方法，輸出：

```
[]
```

因為還沒存入任何東西xd

打開Postman新增一個測試，



選POST方法，Path輸入：http://127.0.0.1:8000/users/

上行帶剛剛的欄位：

```json
{
    "user_name":"kiki",
    "password":"mima",
    "name":"kiki chen"
}
```

送出！同時這支API會顯示剛剛輸入的Data

```json
{
    "user_name":"kiki",
    "password":"mima",
    "name":"kiki chen"
}
```

再去一次GET方法 http://127.0.0.1:8000/users（或加進Postman測試），我們會看到：

```json
[
    {
        "user_name": "kiki",
        "password": "mima",
        "name": "kiki chen"
    }
]
```
---
### 其他or疑難雜症
### ⭐存檔馬上變！

跟Java Web不太一樣，存檔就會馬上生效

### ⭐PyCharm亂抖紅線`=_=╬`
> Unresolved reference

[解法](https://blog.51cto.com/u_15127558/4519981)

---

## 序列化器（20230607）
### 前言
- DRF是啥
  - DRF = Django RESTful Framework
  - [官方網站](https://www.django-rest-framework.org/)
- 話說我覺得我剛開始的學習有點太鑽牛角尖了，先會基本使用就是很棒的一步了，至於底層框架如何運作之後慢慢探索
  - 先努力看看能不能**使用**它達成想要的效果即可

---

> 20230612更新：因為我發現以前收藏的[大江狗](https://pythondjango.cn/django/rest-framework/1-RESTfull-API-why-DRF/)...超會教！！超有自信！！所以決定—

### 跟著大江狗學序列化跟DRF
### ⭐Python內建的序列化
```py
a = 1
json.dump({"num": a}) # 這效果感覺跟JS的JSON物件有點像
```
輸出的JSON字串內容
```json
{"num": 1}
```
Django的序列化方法先略過

### ⭐⭐對比：DRF的序列化
- [以博客为例使用DRF的序列化器和基于函数的视图开发API](https://pythondjango.cn/django/rest-framework/2-first-blog-API/)

---

#### 其他資料
- 今年四月收藏的[django-rest-framework-tutorial](https://github.com/twtrubiks/django-rest-framework-tutorial)也是很好的手把手教學

---
## API狀態碼初步處理（20230608）

經過多方研究跟詢問，大概知道如何在`view`替Data外包一層code跟message，還有藉由例外處理打印對應的狀態

```python
from django.http import JsonResponse
from django.db import transaction
from rest_framework.generics import GenericAPIView
from myapp.serializers import UserSerializer
from myapp.models import User

class UsersView(GenericAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer

    def get(self, request, *args, **kwargs):
        users = self.get_queryset()
        serializer = self.serializer_class(users, many=True)
        data = serializer.data
        response_data = {
            "message": "Success",
            "code": "SUCCESS,
            "data": data
        }
        return JsonResponse(response_data, safe=False)

    def post(self, request, *args, **kwargs):
        data = request.data
        try:
            serializer = self.serializer_class(data=data)
            serializer.is_valid(raise_exception=True)
            with transaction.atomic():
                serializer.save()
            data = serializer.data
            response_data = {
                "message": "Success",
                "code": "SUCCESS",
                "data": data
            }

        except Exception as e:
            response_data = {
                "message": "Something throw a exception.",
                "code": "SOME_EXCEPTION",
                "error": str(e) # 不過比起這樣打印，我比較想記入日誌就好
            }
        return JsonResponse(response_data)
```

打GET方法輸出：

```json
{
    "message": "Success",
    "code": "SUCCESS",
    "data": [
        {
            "user_name": "kiki",
            "password": "mima",
            "name": "kiki chen"
        },
        {
            "user_name": "kiki2",
            "password": "mima",
            "name": "kiki chen"
        }
    ]
}
```

打POST方法但製造一個例外：

```json
{
    "message": "Something throw a exception.",
    "code": "SOME_EXCEPTION",
    "error": "{'user_name': [ErrorDetail(string='user with this user name already exists.', code='unique')], 'name': [ErrorDetail(string='This field is required.', code='required')]}"
}
```

---

### 小記

但我想想這樣之後app一多，這段程式是不是很重複，而且既然**response_data**是用寫出來的，內容可以不統一，能不能拆處來封裝跟做全域掌控呢，想想想...（不過也要顧慮Python的開發風格）

另外日誌紀錄也是蠻重要的......

---

## DB ORM（20230723）
- [models ID自增](https://blog.csdn.net/diyiday/article/details/106002519)
- [models 欄位非必填](https://blog.csdn.net/u012798683/article/details/104713062)
- [時間與快樂(?) Model欄位類型介紹(上) 江狗(Django) 鐵人Day20](https://ithelp.ithome.com.tw/articles/10302021)
- [Enum欄位](https://stackoverflow.com/questions/54802616/how-can-one-use-enums-as-a-choice-field-in-a-django-model)
  - [案外案](https://stackoverflow.com/questions/12995888/name-is-not-defined)
- [外鍵](https://ithelp.ithome.com.tw/articles/10295389)

---

## .gitignore乾貨（20230722）
起因是因為從GitHub import進Replit的Django專案想推版了，但沒寫`.gitignore`，看到旁邊清單一萬塊兩萬個檔案，呃...當然不行r

一開始是參考[這篇](https://www.jianshu.com/p/13612fb4b224)寫（說實話又學到不少0.0），但寫到一半發現還有八千多個檔案擋不掉Orz

好吧，後來犯懶了，而且網路上竟然有－[Django .gitignore懶人包！！](https://djangowaves.com/tips-tricks/gitignore-for-a-django-project/)，就直接拿來用啦（喂www），效果超顯著的'w'

不過我看到Poetry相關的檔案沒擋掉，看完這篇[討論](https://stackoverflow.com/questions/60725232/what-files-directories-should-i-add-to-gitignore-when-using-poetry-the-python)，得知它是類似Java Maven `pom.xml`的角色~~謎之音：你到昨天那刻為止都還沒發現Django也有套件管理齁~~，為了讓大家都能鎖定相同的依賴版本，當然是要一起推上去的～

---

## Replit配置相關：如何在Replit運行從Github import的Django專案（20230629）
### 一、修改按下run的操作行為
#### （一）[官方教學](https://blog.replit.com/deploying-django)，快狠準QQ
#### （二）我的做法，改`.replit`設定檔
在檔案搜尋框輸入.，找到一個叫`.replit`的檔案，貼上這個設定檔**（基底來自Replit官方的Django範本）**，須注意由於我的`manage.py`在FirstProject的目錄下方，所以path才這樣設置，不然正常應該第二個字串帶`manage.py`就好

```python
# The command that runs the program.
run = ["python3", "FirstProject/manage.py", "runserver", "0.0.0.0:3000"]
# The primary language of the repl. There can be others, though!
language = "python3"
# The main file, which will be shown by default in the editor.
entrypoint = "FirstProject/manage.py"
# A list of globs that specify which files and directories should
# be hidden in the workspace.
hidden = ["venv", ".config", "**/__pycache__", "**/.mypy_cache", "**/*.pyc"]
# Specifies which nix channel to use when building the environment.
[nix]
channel = "stable-21_11"
# Per-language configuration: python3
[languages.python3]
# Treats all files that end with `.py` as Python.
pattern = "**/*.py"
# Tells the workspace editor to syntax-highlight these files as
# Python.
syntax = "python"
  # The command needed to start the Language Server Protocol. For
  # linting and formatting.
  [languages.python3.languageServer]
  start = ["pyls"]
# The environment variables needed to correctly start Python and use the
# package proxy.
[env]
VIRTUAL_ENV = "/home/runner/${REPL_SLUG}/venv"
PATH = "${VIRTUAL_ENV}/bin"
PYTHONPATH="${VIRTUAL_ENV}/lib/python3.8/site-packages"
REPLIT_POETRY_PYPI_REPOSITORY="https://package-proxy.replit.com/pypi/"
MPLBACKEND="TkAgg"
# Enable unit tests. This is only supported for a few languages.
[unitTest]
language = "python3"
# Add a debugger!
[debugger]
support = true
  # How to start the debugger.
  [debugger.interactive]
  transport = "localhost:0"
  startCommand = ["dap-python", "main.py"]
    # How to communicate with the debugger.
    [debugger.interactive.integratedAdapter]
    dapTcpAddress = "localhost:0"
    # How to tell the debugger to start a debugging session.
    [debugger.interactive.initializeMessage]
    command = "initialize"
    type = "request"
      [debugger.interactive.initializeMessage.arguments]
      adapterID = "debugpy"
      clientID = "replit"
      clientName = "replit.com"
      columnsStartAt1 = true
      linesStartAt1 = true
      locale = "en-us"
      pathFormat = "path"
      supportsInvalidatedEvent = true
      supportsProgressReporting = true
      supportsRunInTerminalRequest = true
      supportsVariablePaging = true
      supportsVariableType = true
    # How to tell the debugger to start the debuggee application.
    [debugger.interactive.launchMessage]
    command = "attach"
    type = "request"
      [debugger.interactive.launchMessage.arguments]
      logging = {}
# Configures the packager.
[packager]
# Search packages in PyPI.
language = "python3"
# Never attempt to install `unit_tests`. If there are packages that are being
# guessed wrongly, add them here.
ignoredPackages = ["unit_tests"]
  [packager.features]
  enabledForHosting = false
  # Enable searching packages from the sidebar.
  packageSearch = true
  # Enable guessing what packages are needed from the code.
  guessImports = true
```

可以看到它改寫了run按鈕的行為，也可以寫成
```py
run = "python FirstProject/manage.py runserver 0.0.0.0:3000"
```

[寫法出處](https://replit.com/talk/ask/Imported-a-django-web-app-from-github-and-the-server-runs-but-cant-connect-in-browser-window/119598)

所以說以後在Replit撰寫或import其他語言的專案，如果不是用範本建立，也可以透過修改設定檔改變按下run▶️符號的行為

### 二、然後還要把API網址加到`setting.py`的[allowed hosts](https://docs.djangoproject.com/en/1.11/ref/settings/#allowed-hosts)中

像我的要這樣寫：

```py
ALLOWED_HOSTS = ['ourdrf.chi200706.repl.co']
```

---

> 補充

用Replit的Django範本生成之專案，因為`setting.py`中SECRET_KEY的部分被寫成這樣：

```py
SECRET_KEY = os.getenv('SECRET_KEY')
```

所以要切到Tools->Secrets下方新增key，產生方法有寫在範本建立之初的`README.md`

```shell
python # 進python
import secrets # 引進secrets 
secrets.token_urlsafe(32) # 產生token
```

其實平常推Django專案可能不小心暴露SECRET_KEY，這個範本做了很好的示範，多學到了！

---

## DRF例外Handler（20230608）

### 閱讀資料

功能想像：

在view中拋出例外

- [Python Raise an Exception](https://www.w3schools.com/python/gloss_python_raise.asp)

被DRF的handler接住

- [官方文件](https://www.django-rest-framework.org/api-guide/exceptions/)
  - [網友討論](https://stackoverflow.com/questions/60426446/global-exception-handling-in-django-rest-framework)
