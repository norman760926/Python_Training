# DB migrate

<br>

什麼事 DB migrate ? 我們早期一般開發 web 應用，不借住框架的話，一般都是先開到 db 開表，再連到 db 存取測試。有了 DB migrate 之後，開表這種事情在我門開發階段可以根據 python code 的物件動態產表。

<br>

## 關於 DB 連線

* Django 可以連線許多 DB，建立 Django 時，預設使用 SQLite，可以在根目錄下看到一個 db.sqlite3 文件。這邊也使用 SQLite 講解，如果要換 db 請參閱官方文件 :

    https://docs.djangoproject.com/en/2.2/ref/databases/#third-party-notes

<br>

## 建立 Model

* 我們將創建兩個 Model：Question 和 Choice。Question 有一個問題和出版日期。Choice 有兩個 `str`：選擇的文本和投票提示。每個 Choice 都關聯 Question。

* 編輯 polls/models.py : 

    ```py
    from django.db import models


    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')


    class Choice(models.Model):
        question = models.ForeignKey(Question, on_delete=models.CASCADE)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)
    ```

    有了以上的 code 我們就可以讓 Django 幫我門建立 table 了 !

* 接下來我們要這個 model.py 設定註冊到 mysite/setting.py 中。

    編輯 mysite/setting.py 的 `INSTALLED_APPS` 區塊 : 

    ```py
    ...

    INSTALLED_APPS = [
        'polls.apps.PollsConfig',  # 加入這一段
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]

    ...
    ```

    為甚麼事 polls.apps.PollsConfig 呢 ? 可以去 polls/apps.py 看一下 polls 應用的設定檔 class 就叫 PollsConfig，所以我們要這樣註冊 Model。

* 以上做完之後，在 cmd 運行以下片段 : 

    ```bash
    python manage.py makemigrations polls
    ```

    通過運行makemigrations，我們告訴 Django 對模型進行一些修改（在本例中，進行了新增），並且希望將修改結果存儲。

    最後，我們讓 Django 執行 db migrate。

    ```bash
    python manage.py migrate
    ```

    之後就可以到 SQLite 裡面看一下我們新增的 table 喽。

<br>

## 使用 Model

* 首先，我們先為 Model 中的 2 個 class 建立 ``__str__()`` 方法，這樣方便我們呼叫查看 : 

    ```py
    class Question(models.Model):
        # ...
        def __str__(self):
            return self.question_text

    class Choice(models.Model):
        # ...
        def __str__(self):
            return self.choice_text
    ```

* 我們再幫 Question 添加一個簡單的查詢功能 : 

    ```py
    import datetime

    from django.db import models
    from django.utils import timezone


    class Question(models.Model):
        # ...
        def was_published_recently(self):
            return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
    ```

    <br>

## 使用單元測試

* 接下來，我們利用 Django 的單元測試 （`TestCase`） 來玩一下 SQLite 吧。

* 打開 /polls/tests.py，這個文件是用來寫這個模組的單元測試。直接貼上下方範例 : 

    ```py
    from django.test import TestCase
    from django.utils.datetime_safe import datetime

    from polls.models import Question, Choice


    class PollTestCase(TestCase):
        def setUp(self):
            question = Question(question_text="你午餐吃什麼 ?", pub_date=datetime.now())
            question.save()
            c1 = Choice(question=question, choice_text="沒有吃", votes=3)
            c2 = Choice(question=question, choice_text="吃葷", votes=10)
            c3 = Choice(question=question, choice_text="吃素", votes=4)
            c1.save()
            c2.save()
            c3.save()

        def test_create_Question_and_Choices(self):
            question = Question.objects.get(question_text="你午餐吃什麼 ?")
            choices = Choice.objects.all().filter(question=question)
            print(question)
            print(choices)
            self.assertEqual(question.question_text, '你午餐吃什麼 ?')
            self.assertEqual(len(choices), 3)

        def test_Question_was_published_recently(self):
            question = Question.objects.get(question_text="你午餐吃什麼 ?")
            print("was_published_recently : ", question.was_published_recently())
            self.assertEqual(question.was_published_recently(), True)
    ```

    先解釋一下我們做了什麼，首先看 `setup()` 方法，這個方法是單元測試的前置作業，再執行所有測試前會先跑 `setUp()` 方法，所已我們就先把建立測試資料的步驟放在這裡。這裡所建立的資料只會存在這個測試周期結束，便會自動刪除。

    然後我們定義了 2 個方法分別測試資料建立，以及查詢，還有測試自定的方法。

    <br>

* 因為 PyCharm 對 Django 單元測試的支援沒有做的很好，所以我們這邊手動去觸發測試 : 

    cd 到 /mysite 資料夾下並執行以下命令

    ```bash
    manage.py test polls.tests
    ```

    執行結果如下 : 

    ```bash
    Creating test database for alias 'default'...

    System check identified no issues (0 silenced).

    C:\Users\User\Desktop\PycharmProjects\my-site\venv\lib\site-packages\django\db\models\fields\__init__.py:1368: RuntimeWarning: DateTimeField Question.pub_date received a naive datetime (2020-02-24 15:54:09.353398) while time zone support is active. RuntimeWarning)

    was_published_recently :  True

    .C:\Users\User\Desktop\PycharmProjects\my-site\venv\lib\site-packages\django\db\models\fields\__init__.py:1368: RuntimeWarning: DateTimeField Question.pub_date received a naive datetime (2020-02-24 15:54:09.359398) while time zone support is active. RuntimeWarning)

    你午餐吃什麼 ?
    <QuerySet [<Choice: 沒有吃>, <Choice: 吃葷>, <Choice: 吃素>]>
    .
    ----------------------------------------------------------------------
    Ran 2 tests in 0.010s

    OK
    Destroying test database for alias 'default'...

    ```

    可以看到 log 資訊，開頭顯示建立測試資料，再來試分別測試方法，最後是刪除測試資料。
