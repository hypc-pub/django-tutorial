# 编写第一个Django应用 -- Models & Admin site

## 数据库设置

打开`mysite/settings.py`文件，这里面都是一些Django配置。

默认情况下，Django使用SQLite数据库，当然你也可以替换成其他数据库。

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

* ENGINE: 可以是`django.db.backends.sqlite3`、`django.db.backends.postgresql`、
    `django.db.backends.mysql`、`django.db.backends.oracle`，也可以是[其他可能的值][also available]；
* NAME: 数据库名称。如果使用SQLite，那它是数据库文件。

如果使用SQLite数据库之外的其他数据库，那么还需要一些其他配置，如`USER`、`PASSWORD`、`HOST`。
具体参见[DATABASES][]。

数据库设置完之后，需要将[TIME_ZONE][]设置为你所在的时区。

另外还需要注意[INSTALLED_APPS][]这个配置，它包含在这个项目中用到的App，里面有一些默认的设置：

* `django.contrib.admin`: Admin site；
* `django.contrib.auth`: 一个授权系统；
* `django.contrib.contenttypes`: content type框架；
* `django.contrib.sessions`: session框架；
* `django.contrib.messages`: 消息框架；
* `django.contrib.staticfiles`: 管理静态文件的框架。

其中一些App用到了数据库表，所以在使用之前需要在数据库中创建这些表，使用下面命令：

```bash
python manage.py migrate
```

[also available]: https://docs.djangoproject.com/en/2.1/ref/databases/#third-party-notes
[DATABASES]: https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-DATABASES
[INSTALLED_APPS]: https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-INSTALLED_APPS
[TIME_ZONE]: https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-TIME_ZONE
[INSTALLED_APPS]: https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-INSTALLED_APPS

## 创建models

现在开始定义models，它本质上可以认为是数据库表结构。

打开`polls/models.py`文件，输入以下内容：

```python
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

这里定义了两个model：`Question`、`Choice`，他们都是[django.db.models.Model][]子类。

每个字段都是[Field][]类的实例。例如[CharField][]、[DateTimeField][]等。

[django.db.models.Model]: https://docs.djangoproject.com/en/2.1/ref/models/instances/#django.db.models.Model
[Field]: https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.Field
[CharField]: https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.CharField
[DateTimeField]: https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.DateTimeField

## 激活models

在项目需要添加polls app，需要在[INSTALLED_APPS][]中添加`polls.apps.PollsConfig`类，
打开`mysite/settings.py`文件，并按如下配置：

```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

设置[INSTALLED_APPS][]后执行下面命令：

```bash
$ python manage.py makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py:
    - Create model Choice
    - Create model Question
    - Add field question to choice
```

通过运行`makemigrations`，告诉Django已对model进行了一些更改（在这种情况下是创建了新模型），并且将更改存储为迁移文件。

可以使用[migrate][]命令自动管理数据库，也可以使用[sqlmigrate][]命令导出SQL语句。
执行[migrate][]命令，效果如下：

```bash
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Rendering model states... DONE
  Applying polls.0001_initial... OK
```

[migrate]: https://docs.djangoproject.com/en/2.1/ref/django-admin/#django-admin-migrate
[sqlmigrate]: https://docs.djangoproject.com/en/2.1/ref/django-admin/#django-admin-sqlmigrate

## 使用[database API][]

可以使用django交互式命令行来使用[database API][]，执行命令如下：

```bash
python manage.py shell
```

现在，可以使用[database API][]：

```python
>>> from polls.models import Choice, Question
# 现在还没有任何Question
>>> Question.objects.all()
<QuerySet []>

# 创建一个Question并保存
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())
>>> q.save()

>>> q.id
1
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2018, 10, 21, 14, 14, 51, 932745, tzinfo=<UTC>)

# 修改q.question_text值并保存
>>> q.question_text = "What's up?"
>>> q.save()

# 查看所有的Question
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

等等，`<Question: Question object (1)>`显示效果并不是很友好，
打开`polls/models.py`文件，并为`Question`和`Choice`添加`__str__`函数：

```python
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```

也可以为model添加自定义函数：

```python
from datetime import timedelta

from django.db import models
from django.utils import timezone


class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - timedelta(days=1)
```

然后再次执行命令`python manage.py shell`打开django shell：

```python
>>> from polls.models import Choice, Question

# Make sure our __str__() addition worked.
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a
# shortcut for primary-key exact lookups.
# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
# of available choices and returns the new Choice object. Django creates
# a set to hold the "other side" of a ForeignKey relation
# (e.g. a question's choice) which can be accessed via the API.
>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>> q.choice_set.all()
<QuerySet []>

# Create three choices.
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question
<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
```

[database API]: https://docs.djangoproject.com/en/2.1/topics/db/queries/

## Django Admin

### 创建管理员帐号

首先创建管理员帐号：

```bash
python manage.py createsuperuser
```

然后按照提示输入用户名、邮箱、密码。

### 启动服务并登录

执行命令：

```bash
python manage.py runserver
```

然后打开浏览器访问网页 http://127.0.0.1:8000/admin/ ，输入上一步创建的帐号密码登录。

### 让polls在admin site中可见

登录之后，我们在admin site页面中并没有发现polls app。

修改`polls/admin.py`文件：

```python
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

这时候可以看到在admin site页面中看到polls app，并在下面看到Questi model。
