# 编写第一个Django应用 -- Create a project

## 创建一个项目

直接使用下面命令创建一个项目：

```bash
django-admin startproject mysite
```

命令执行成功之后会在当前目录下创建一个`mysite`的项目，项目结构如下：

```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

* 外层的`mysite/`目录是项目的根目录；
* `manage.py`文件可以认为是django项目入口，
    可以通过执行`python manage.py <command>`命令与django项目进行各种交互；
* 内层的`mysite/`目录是一个真实的Python package，它用来放置项目；
* `mysite/__init__.py`一个空白的文件，它用来告诉Python当前目录是一个Python package；
* `mysite/settings.py`，项目的配置文件，参考[Django settings][]；
* `mysite/urls.py`，负责把URL模式映射到应用程序，参考[URL dispatcher][]；
* `mysite/wsgi.py`，兼容WSGI的Web服务器的入口文件，参考[How to deploy with WSGI][]。

## 运行Django服务

在项目根目录下运行下面任一命令启动服务：

```bash
# 默认启动绑定127.0.0.1
python manage.py runserver
# 绑定到127.0.0.1:8080
python manage.py runserver 8080
# 绑定到0.0.0.0:8080
python manage.py runserver 0.0.0.0:8080
```

## 创建第一个App -- Polls

使用下面命令创建一个App：

```bash
python manage.py startapp polls
```

命令执行成功后，会在项目根目录下新建一个`polls`目录，目录结构如下：

```
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

## 编写第一个view

打开文件`polls/view.py`，并输入以下内容：

```python
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

新建文件`polls/urls.py`，并输入以下内容：

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

然后打开`mysite/urls.py`文件，并输入下面内容：

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

所有文件编写完成之后，启动服务：

```bash
python manage.py runserver
```

最后你可以通过打开 http://localhost:8000/polls/ 来访问服务。

[path][django.urls.path]函数可以传入4个参数，两个必选参数`route`、`view`，两个可选参数`kwargs`、`name`。

### path参数：route

route是一个包含URL模式的字符串。处理请求时，Django从urlpatterns中的第一个模式开始，
沿着列表向下移动，将请求的URL与每个模式进行比较，直到找到匹配的模式。

### path参数：view

当Django找到匹配的模式时，它调用指定的视图函数，其中HttpRequest对象作为第一个参数，
并且路由中的任何“捕获”值作为关键字参数。

### path参数：kwargs

任意关键字参数可以在字典中传递到目标视图。

### path参数：name

命名您的URL可让您从Django的其他地方明确地引用它，尤其是在模板中。
此强大功能允许您在仅触摸单个文件的同时对项目的URL模式进行全局更改。

[Django settings]: https://docs.djangoproject.com/en/2.1/topics/settings/
[URL dispatcher]: https://docs.djangoproject.com/en/2.1/topics/http/urls/
[How to deploy with WSGI]: https://docs.djangoproject.com/en/2.1/howto/deployment/wsgi/
[django.urls.path]: https://docs.djangoproject.com/en/2.1/ref/urls/#django.urls.path
