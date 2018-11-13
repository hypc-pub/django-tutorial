# In the end

最后，总结一下，编写django项目的过程：

1. 首先使用`django-admin startproject mysite`命令创建一个项目；
2. 然后使用`python manage.py startapp polls`命令创建一个app；
3. 在`polls/models.py`文件中定义好Model；
4. 在`polls/views.py`文件中编写View；
5. 在`polls/urls.py`文件中配置好路由规则；
6. 在`mysite/settings.py`文件的`INSTALLED_APPS`中配置`polls`模块；
7. 在`mysite/urls.py`文件中加载`polls`模块的路由规则；
8. 执行`python manage.py makemigrations`命令生成数据库迁移文件；
9.  执行`python manage.py migrate`命令生成数据库表；
10. 在`polls/tests.py`文件中编写测试用例；
11. 执行`python manage.py test`命令执行测试用例。

至此，一个简单的django项目编写完成。

完整的项目结构如下：

```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
    polls/
        __init__.py
        admin.py
        migrations/
            __init__.py
            0001_initial.py
        models.py
        tests.py
        urls.py
        views.py
```
