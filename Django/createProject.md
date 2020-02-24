# 建立專案

<br>

## 下載

```bash
pip install django
```

## 建立專案

```bash
django-admin startproject mysite
```

建立好專案後，資料夾結構如下 : 

```bash
my-site/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

## 啟動 server

```bash
python manage.py runserver
```

在 http://127.0.0.1:8000 上就可以看到架好的網站了