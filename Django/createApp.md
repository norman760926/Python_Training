# create app

<br>

## 在 mysite 裡建立一個 app 應用

```bash
python manage.py startapp polls
```

建立好後的資料夾結構 :

```bash
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

## 開始寫 view

* 編輯 polls/views.py

    ```py
    from django.http import HttpResponse


    def index(request):
        return HttpResponse("Hello, world. You're at the polls index.")
    ```

* 接下來我們要寫一個 router，建立 polls/urls.py

    ```py
    from django.urls import path

    from . import views

    urlpatterns = [
        path('', views.index, name='index'),
    ]
    ```
* 其實 Django 只任一個 router，就是在專案根目錄下的 urls.py 文件，我們要註冊 polls/urls.py 到主 router 中:

    編輯 mysite/urls.py

    ```py
    from django.contrib import admin
    from django.urls import include, path

    urlpatterns = [
        path('polls/', include('polls.urls')),  # 代入 polls.url.py
        path('admin/', admin.site.urls),
    ]
    ```
    改好後，重啟 server，連線到 http://127.0.0.1:8000/polls/