---
title: django使用
date: 2022-01-19 20:16:29
categories:
tags:
---

一边写 docker 一边写 django 吧，欠债还钱啊。首先对照官方文档，它们已经不那么 shit 了

https://docs.djangoproject.com/zh-hans/4.0/intro/tutorial01/

参照这个，manage.py 上面的那一层目录可以随意重命名

叮咚，__init__.py 的妙用

https://www.jianshu.com/p/73f7fbf75183

方便导入多个的时候执行

app 负责具体功能实现，project 本身装载，然而最高层的 url.py 才是标记哪里到哪里的，视图大概是会体现什么？

函数 [`include()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.include) 允许引用其它 URLconfs。每当 Django 遇到 [`include()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.include) 时，它会截断与此项匹配的 URL 的部分，并将剩余的字符串发送到 URLconf 以供进一步处理。

所以只要包含了就行？

```
path('polls/', include('polls.urls')),
```

[`path()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.path) 参数： `route`[¶](https://docs.djangoproject.com/zh-hans/4.0/intro/tutorial01/#path-argument-route)

`route` 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 `urlpatterns` 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。

这些准则不会匹配 GET 和 POST 参数或域名。例如，URLconf 在处理请求 `https://www.example.com/myapp/` 时，它会尝试匹配 `myapp/` 。处理请求 `https://www.example.com/myapp/?page=3` 时，也只会尝试匹配 `myapp/`。

[`path()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.path) 参数： `view`[¶](https://docs.djangoproject.com/zh-hans/4.0/intro/tutorial01/#path-argument-view)

当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 [`HttpRequest`](https://docs.djangoproject.com/zh-hans/4.0/ref/request-response/#django.http.HttpRequest) 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。稍后，我们会给出一个例子

得了一切都很麻烦，从 model 开始做吧，model 其实就是写数据库，这次是三个模型

https://docs.djangoproject.com/zh-hans/4.0/topics/db/examples/many_to_one/

```python
class Article(models.Model):
    headline = models.CharField(max_length=100)
    pub_date = models.DateField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)

    def __str__(self):
        return self.headline

    class Meta:
        ordering = ['headline']
```

总是忍不住要打分号……anyway，meta不知道是啥意思，反正内容这样就可以了

去记录时间可以用 datetimefield `dateRecorded = models.DateTimeField()`

使用 TimeStampedModel 它会自动维系 created and modified field，使用它替代原来的 model

##### serializer

嘛，要来回传送数据所以要互相转换，这里是用新建了一个 serializer py 文件的方式

https://www.liujiangblog.com/course/django/171

[1 - Serialization - Django REST framework](https://www.django-rest-framework.org/tutorial/1-serialization/#using-modelserializers)

看看这个……那么这里要直接使用隔壁 model 的定义

```python
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = ['id', 'title', 'code', 'linenos', 'language', 'style']
```

这个返回的话就是根据 model snippet 返回的 json

```python
# blog/serializers.py
class ArticleSerializer(serializers.ModelSerializer):


    class Meta:
        model = Article
        fields = '__all__'
        read_only_fields = ('id', 'author', 'create_date')
```

回复是

![](https://pythondjango.cn/django/rest-framework/4-DRF-serializer-advanced.assets/6.png)

然后可以在上面复写 read-only

```python
class UserFeedbackSerializer(serializers.ModelSerializer):
    """Serializer for user feedbacks."""
    reply = UserFeedbackReplySerializer(
        read_only=True
    )

    class Meta:
        """Metadata for user feedback serializer."""
        model = UserFeedback
        fields = ('id', 'content', 'reply', 'type', 'created', 'modified')
        extra_kwargs = {
            'content': {'required': True},
            'type': {'required': True},
        }

    def create(self, validated_data):
        """Create a feedback based on provided data and data from the serializer context."""
        request = self.context.get("request")
        user = request.user
        feedback = UserFeedback.objects.create(
            content=validated_data['content'],
            type=validated_data['type'],
            user=user
        )

        return feedback
```

现在要做的是 create plan of action，参考那边的 create reply

先要创造不包含任何的，comment？

先写 comment，然后是 milestone，然后 pla，comment 和 milestone 回复 pla

metadata 里面是包含什么，记得要有 created, id,  modified, 这是本来就有的，还要搭配什么必须有，好像可以用 `__all__` ? 不管了

##### 设置 type

```python
    class YearInSchool(models.TextChoices):
        FRESHMAN = 'FR', _('Freshman')
        SOPHOMORE = 'SO', _('Sophomore')
        JUNIOR = 'JR', _('Junior')
        SENIOR = 'SR', _('Senior')
        GRADUATE = 'GR', _('Graduate')

    year_in_school = models.CharField(
        max_length=2,
        choices=YearInSchool.choices,
        default=YearInSchool.FRESHMAN,
    )
```

'FR', 'SO' 是实际值，Freshmen 是人可读形式，展开来是这样

```python
class Student(models.Model):
    FRESHMAN = 'FR'
    SOPHOMORE = 'SO'
    JUNIOR = 'JR'
    SENIOR = 'SR'
    GRADUATE = 'GR'
    YEAR_IN_SCHOOL_CHOICES = [
        (FRESHMAN, 'Freshman'),
        (SOPHOMORE, 'Sophomore'),
        (JUNIOR, 'Junior'),
        (SENIOR, 'Senior'),
        (GRADUATE, 'Graduate'),
    ]
    year_in_school = models.CharField(
        max_length=2,
        choices=YEAR_IN_SCHOOL_CHOICES,
        default=FRESHMAN,
    )
```

FRESHMEN 这些是常量，给这些值赋予常量方便之后引用？

`_('personal')` 翻译，象征需要有相应钩子

##### view

这里是使用了 apiview 还有啥……modelview,这个是最高抽象，让我看看怎么用

[Generic views - Django REST framework中文站点](https://q1mi.github.io/Django-REST-framework-documentation/api-guide/generic-views_zh/)

继承 mixin 或者后面的 [ModelViewSet](https://q1mi.github.io/Django-REST-framework-documentation/api-guide/viewsets_zh/#modelviewset) 来enable 基本的创建，修改等等活，引用 serializer 

草，要查找大概要用 #### [`filter_queryset(self, queryset)`](https://www.django-rest-framework.org/api-guide/generic-views/#filter_querysetself-queryset) 让我康康哪个继承了

##### test case

使用 test 开头的测试 func，然后 snake_case， setUp 和 tearDown 在开始和结束的时候放去 set data，然后用 `assertEqual` 来测试是否一致.

```python
class TestCommentMentor(TestCase):
    """
    testing for comment
    """ 
    def test_create(self):
        """
        test mentor create comment
        """

        data = {
            "content" : "Test data"
        }

        serializer = CommentMentorSerializer()
        result = serializer.create(data)

        self.assertEqual(result.content, data['content'])
```

先设置 data，再引入 serializer，然后创建或者 update，再对比是否一致,update 的话先在 func 里面 create 再 update

create 直接用 model 去 create

```python
    def test_update(self):
        """
        Test updating replies for a user feedback.
        """
        data = {
            "content": "Updated Content",
        }

        update_request = APIRequestFactory().post('fakepath')
        update_request.user = self.other_admin
        force_authenticate(update_request, user=self.other_admin)

        reply = UserFeedbackReply.objects.create(
            feedback=self.feedback,
            content="Initial Content",
            admin=self.admin
        )

        serializer = UserFeedbackReplyAdminSerializer(instance=reply, context={
            "request": update_request,
        })

        serializer.update(reply, data)

        self.assertEqual(reply.admin, self.other_admin)
        self.assertEqual(reply.content, data['content'])
```

` python -m unittest test_serializers.py `

测试

传入的 fk 如果是直接关联到实力就要用实例关联 qwq



#### view
