# 编写第一个Django应用 -- Views & Urls & Forms

## 编写更多的View

我们的View都是基于[Base views][]而编写的。

打开`polls/views.py`文件，将文件内容修改如下：

```python
from django.http import HttpResponse
from django.views import View

class IndexView(View):
    def get(self, request):
        return HttpResponse("Hello, world. You're at the polls index.")

class DetailView(View):
    def get(self, request, question_id):
        return HttpResponse("You're looking at question %s." % question_id)

class ResultsView(View):
    def get(self, request, question_id):
        response = "You're looking at the results of question %s."
        return HttpResponse(response % question_id)

class VoteView(View):
    def get(self, request, question_id):
        return HttpResponse("You're voting on question %s." % question_id)
```

然后修改`polls/urls.py`文件内容修改如下：

```python
from django.urls import path

from .views import IndexView, DetailView, ResultsView, VoteView

# ex: /polls/...
urlpatterns = [
    path('', IndexView.as_view(), name='index'),
    path('<int:question_id>/', DetailView.as_view(), name='detail'),
    path('<int:question_id>/results/', ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', VoteView.as_view(), name='vote'),
]
```

现在，可以启动服务，访问 http://127.0.0.1:8000/polls/1/ 查看效果。

[Base views]: https://docs.djangoproject.com/en/2.1/ref/class-based-views/base/

## 让View做一些真实的事情

同样，打开`polls/views.py`文件，修改`IndexView`如下：

```python
from django.http import HttpResponse
from django.views import View

from .models import Question

class IndexView(View):
    def get(self, request):
        latest_question_list = Question.objects.order_by('-pub_date')[:5]
        output = ', '.join([q.question_text for q in latest_question_list])
        return HttpResponse(output)
```

启动服务，访问 http://127.0.0.1:8000/polls/ 查看效果。

## 使用Form

首先，新建文件`polls/forms.py`，创建一个[Form][]类：

```python
from django import forms

class QuestionIndexForm(forms.Form):
    question_text = forms.CharField(max_length=100, label='question_text')
```

然后修改`polls/views.py`文件的`IndexView`：

```python
from django.http import HttpResponse
from django.views import View

from .models import Question
from .forms import QuestionIndexForm

class IndexView(View):
    def get(self, request):
        form = QuestionIndexForm(request.GET)
        if not form.is_valid():
            return HttpResponse('', status=422)

        data = form.cleaned_data
        latest_question_list = Question.objects.filter(
            question_text__contains=data['question_text']).order_by('-pub_date')[:5]
        output = ', '.join([q.question_text for q in latest_question_list])
        return HttpResponse(output)
```

启动服务，访问 http://127.0.0.1:8000/polls/?question_text=What 查看效果。

[Form]: https://docs.djangoproject.com/en/2.1/ref/forms/api/#django.forms.Form
