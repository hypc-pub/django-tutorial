# Django Tutorial

[Django][]是一个开放源代码的Web应用框架，由Python写成。采用了MVC的框架模式，即模型M，视图V和控制器C。

Django的主要目的是简便、快速的开发数据库驱动的网站。
它强调代码复用，多个组件可以很方便的以“插件”形式服务于整个框架，Django有许多功能强大的第三方插件，
你甚至可以很方便的开发出自己的工具包。这使得Django具有很强的可扩展性。
它还强调快速开发和DRY(Do Not Repeat Yourself)原则。

Django基于MVC的设计十分优美：

* 对象关系映射 (ORM,object-relational mapping)：以Python类形式定义你的数据模型，
    ORM将模型与关系数据库连接起来，你将得到一个非常容易使用的数据库API，同时你也可以在Django中使用原始的SQL语句。
* URL 分派：使用正则表达式匹配URL，你可以设计任意的URL，没有框架的特定限定。像你喜欢的一样灵活。
* 模版系统：使用Django强大而可扩展的模板语言，可以分隔设计、内容和Python代码。并且具有可继承性。
* 表单处理：你可以方便的生成各种表单模型，实现表单的有效性检验。可以方便的从你定义的模型实例生成相应的表单。
* Cache系统：可以挂在内存缓冲或其它的框架实现超级缓冲 －－ 实现你所需要的粒度。
* 会话(session)，用户登录与权限检查，快速开发用户会话功能。
* 国际化：内置国际化系统，方便开发出多种语言的网站。
* 自动化的管理界面：不需要你花大量的工作来创建人员管理和更新内容。Django自带一个ADMIN site,类似于内容管理系统。

[Django]: https://www.djangoproject.com/
[Django Docs]: https://docs.djangoproject.com/
