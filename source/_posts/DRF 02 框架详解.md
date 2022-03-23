---
title: DRF 详解
date: 2019-03-05
comments: false
toc: true
categories: "DRF 框架"
tags: [DRF详解]
---



# DRF 框架



## DRF 框架知识总览

### 接口(api)

#### 什么是接口

- 定义

`前台与后台进行信息交互的媒介 -- url链接`

#### 接口组成

- url链接  - `http://127.0.0.1:9000/api/users/`

- 请求方式 - get(查), post(增), put(整体改), patch(局部改), delete(删)

- 请求参数 - 拼接参数, 数据包参数(urlencoded, json, form-data)

- 响应结果 - 响应的 json数据

- 开发阶段接口测试工具

  ```python
  Postman:
      官网下载
  ```

#### 接口文档

##### 为什么要写接口文档

- 作为后台开发者, 要通过`url`将数据返回给前台
- 接口文档是给 后台开发者, 前台开发者, 测试等各个相关项目组同事查看的, 方便团队开发(规则是后台指定的, 文档后台来写)

##### 编写文档方式

- word 编写(不推荐)
- drf 框架插件, 可以根据cbv的类快速生产文档(类必须有注释等信息, 要规范, 不推荐)
- 采用写文档的平台

##### 书写过程

- 先按照开发需要,完成接口的开发.
  - 设置后台`url`链接 
  - 设置请求方式
  - 设置请求数据
  - 设置响应结果
- 选择接口平台 将以上变成文档即可



#### 接口规范

##### 为什么要制定接口规范

- 在前后台分离的情况下, 后台可以采用不同的后台应用, 因为前后台的响应规则是一致的
- 按照一套规范来编写, 后台不管是什么语言, 前台都可以采用一样的交互方式.
- 与之相反,  后台也不用管前台以何种方式开发

##### 通用的接口规范: Restful 接口规范

- url 编写
  - https 协议 -  保证数据的安全性
  - api字眼  - 标识操作的是数据
  - v1/v2 字眼 - 数据的多版本共存
  - 资源复数 - 请求的数据称之为资源 用数据类别的复数
  - 拼接条件 - 过滤群查接口数据 (`https://api.baidu.com/books/?limit=3&ordering=-price`)

- 请求方式
  - /books/ - get - 群查
  - /books/(pk)/ - get - 单查
  - /books/ - post - 单增
  - /books/(pk)/ - put - 单整体改
  - /books/(pk)/ - patch - 单局部改
  - /books/(pk)/ - delete - 单删

- 响应结果

  - 网络状态码与状态信息：2xx | 3xx | 4xx | 5xx
  - 数据状态码：前后台约定规则 - 0:成功 1:失败 2:成功无结果
  - 数据状态信息：自定义成功失败的信息解释(英文)
  - 数据本体：json数据
  - 数据子资源：头像、视频等，用资源的url**链接**

- 基于restful接口规范的接口设计

  ```python
  urlpatterns = [
      # 资源books接口的设计
      url(r'^books/$', views.BookAPIView.as_view()),  # 群查、单增
      url(r'^books/(?P<pk>\d+)/$', views.BookAPIView.as_view()),  # 单查、单删、单(整体|局部)改
  ]
  ```

### FBV  与 CBV : Function|Class Base View

- CBV 比FBV的优势
  - fbv没一个接口都会对应一个函数来响应请求
  - cbv可以将一个资源的增删改查所有操放在一个类中管理，在内部再分方法逐一处理 （高内聚低耦合：六个接口和一个类有关，但都能在类内部处理）

### DRF 框架的基础视图类

#### APIView

##### 请求模块

###### DRF 框架APIView 的请求生命周期 概览

![](https://gitee.com//qilitang/Img/raw/master/img/001.png)

###### 重写as_view方法

- as_view方法完成路由配置，返回配置函数是 csrf_exempt(view)，也就是禁用了csrf认证规则

- 所有继承APIView的子类，都不受csrf认证规则的限制

- 将请求处理的任务交给dispath方法完成

  

  ![](https://gitee.com//qilitang/Img/raw/master/img/20200219181651.png)

##### 解析模块

- 二次封装了原生Django的uwsgi协议的request对象，并做了向下兼容（原来request对象的内容，用现在的request对象都能访问）

- 将所有拼接参数都放在request.query_params中，将所有数据包参数都放在request.data中

- 路由的有名无名分组的数据还是保存在args和kwargs中

- 解析模块在 setting.py自定义解析设置

  ```python
  REST_FRAMEWORK = {
      # 解析模块
      'DEFAULT_PARSER_CLASSES': [
          'rest_framework.parsers.JSONParser',  # json
          'rest_framework.parsers.FormParser',  # urlencoded
          'rest_framework.parsers.MultiPartParser'  # form-data
      ],
  }
  ```

  

  ![](https://gitee.com//qilitang/Img/raw/master/img/1582111555.png)

  ![](https://gitee.com//qilitang/Img/raw/master/img/20200219192701.png)

  ![](https://gitee.com//qilitang/Img/raw/master/img/image-20200219192720875.png)

  ![](https://gitee.com//qilitang/Img/raw/master/img/image-20200219192739414.png)

##### 渲染模块

- 当三大认证模块和自己处理请求的视图逻辑没有出现异常时，会执行响应渲染模块

- 响应的数据会交给渲染模块来完成数据的渲染，渲染方式有两种：Json格式数据渲染、Brower格式数据渲染

- 自定义渲染模块解析配置

  ```python
  REST_FRAMEWORK = {
      # 渲染模块
      'DEFAULT_RENDERER_CLASSES': [
          'rest_framework.renderers.JSONRenderer',
          # 浏览器渲染，上线后会注释，不然浏览器请求接口就暴露了后台语言
          'rest_framework.renderers.BrowsableAPIRenderer',  
      ],
  }
  ```

  

##### 响应模块

###### 响应类: Response

- 参数

  ```python
  """
  def __init__(self, data=None, status=None, template_name=None, headers=None, 								exception=False, content_type=None):
      pass
      
  data：响应的数据 - 空、字符串、数字、列表、字段、布尔
  status：网络状态码
  template_name：drf说自己也可以支持前后台不分离返回页面，但是不能和data共存（不会涉及）
  headers：响应头（不用刻意去管）
  exception：是否是异常响应（如果是异常响应，可以赋值True，没什么用）
  content_type：响应的结果类型（如果是响应data，默认就是application/json，所有不用管）
  """
  ```

- 常见使用

  ```python
  Response(
      data={
          'status': 0,
          'msg': 'ok',
          'result': '正常数据'
      }
  )
  
  Response(
      data={
          'status': 1,
          'msg': '客户端错误提示',
      },
  	status=status.HTTP_400_BAD_REQUEST,
  	exception=True
  )
  ```

  



##### 异常模块

### DRF 核心组件

##### 序列组件

![](https://gitee.com/qilitang/Img/raw/master/img/image-20200219204826824.png)

###### 序列化基类 BaseSerializer 

- 初始化参数

  ```python
  def __init__(self, instance=None, data=empty, **kwargs):
      
  	# instance： 对象类型数据赋值给instance形参
      # data：请求来的数据赋值给data 形参
      # kwargs：内部有三个属性：many、partial、context
     		# many：操作的对象或数据，是单个的还是多个的
      	# partial：在修改需求时使用，可以将所有校验字段required校验规则设置为False
      	# context：用于视图类和序列化类直接传参使用
        
  # 常见使用
  # 单查接口
  UserModelSerializer(instance=user_obj)
  
  # 群查接口
  UserModelSerializer(instance=user_query, many=True)
  
  # 增接口
  UserModelSerializer(data=request.data)
  
  # 整体改接口
  UserModelSerializer(instance=user_obj, data=request.data)
  
  # 局部改接口
  UserModelSerializer(instance=user_obj, data=request.data, partial=True)
  
  # 删接口，用不到序列化类
  ```


###### 单表序列化

- models.py

  ```python
  from django.db import models
  from django.conf import settings
  class User(models.Model):
      SEX_CHOICES = ((0, '男'), (1, '女'))
      name = models.CharField(max_length=64, verbose_name='姓名')
      password = models.CharField(max_length=64)
      age = models.IntegerField()
      height = models.DecimalField(max_digits=5, decimal_places=2, default=0)
      sex = models.IntegerField(choices=SEX_CHOICES, default=0)
      # sex = models.CharField(choices=[('0', '男'), ('1', '女')])
      icon = models.ImageField(upload_to='icon', default='icon/default.png')
  
      # 自定义序列化给前台的字段
      # 优点：1）可以格式化数据库原有字段的数据 2）可以对外隐藏原有数据库字段名 3）可以直接连表操作
      @property  # 制造插头
      def gender(self):
          return self.get_sex_display()
  
      @property
      def img(self):
          return settings.BASE_URL + settings.MEDIA_URL + self.icon.name
  
      def __str__(self):
          return self.name
  ```

- serializers.py

  ```python
  from rest_framework import serializers
  from . import models
  class UserModelSerializer(serializers.ModelSerializer):
      class Meta:
          # 该序列化类是辅助于那个Model类的
          model = models.User
          # 设置参与序列化与反序列化字段
          # 插拔式：可以选择性返回给前台字段（插头都是在Model类中制造）
          # fields = ['name', 'age', 'height', 'sex', 'icon]
          fields = ['name', 'age', 'height', 'gender', 'img']
  ```

- views.py

  ```python
  from rest_framework.views import APIView
  from rest_framework.response import Response
  
  from . import models, serializers
  
  class UserAPIView(APIView):
      def get(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          if pk:  # 单查
              # 1）数据库交互拿到资源obj或资源objs
              # 2）数据序列化成可以返回给前台的json数据
              # 3）将json数据返回给前台
              obj = models.User.objects.get(pk=pk)
              serializer = serializers.UserModelSerializer(obj, many=False)
              return Response(serializer.data)
  
          else:  # 群查
              # 1）数据库交互拿到资源obj或资源objs
              # 2）数据序列化成可以返回给前台的json数据
              # 3）将json数据返回给前台
              queryset = models.User.objects.all()
              # many操作的数据是否是多个
              serializer = serializers.UserModelSerializer(queryset, many=True)
              return Response(serializer.data)
  
      def post(self, request, *args, **kwargs):
          # 单增
          # 1）从请求request中获得前台提交的数据
          # 2）将数据转换成Model对象，并完成数据库入库操作
          # 3）将入库成功的对象列化成可以返回给前台的json数据（请求与响应数据不对等：请求需要提交密码，响应一定不展示密码）
          # 4）将json数据返回给前台
  
          return Response()
  ```

###### 单表反序列化

- `views.py`

  ```python
  class UserAPIView(APIView):
      def post(self, request, *args, **kwargs):
          # 单增
          # 1）将前台请求的数据交给序列化类处理
          # 2）序列化类执行校验方法，对前台提交的所有数据进行数据校验：校验失败就是异常返回，成功才能继续
          # 3）序列化组件完成数据入库操作，得到入库对象
          # 4）响应结果给前台
          serializer = serializers.UserModelSerializer(data=request.data)
          if serializer.is_valid():
              # 校验成功 => 入库 => 正常响应
              obj = serializer.save()
              return Response({
                  'status': 0,
                  'msg': 'ok',
                  'result': '新增的那个对象'
              }, status=status.HTTP_201_CREATED)
          else:
              # 校验失败 => 异常响应
              return Response({
                  'status': 1,
                  'msg': serializer.errors,
              }, status=status.HTTP_400_BAD_REQUEST)
  ```

- 反序列化

  `serializers.py`

  - 1: 不管是序列化还是反序列化字段，都必须在fields中进行声明，没有声明的不会参与任何过程(数据都会被丢弃)

  - 2: 用 read_only 表示只读，用 write_only 表示只写，不标注两者，代表可读可写

  - 3: 自定义只读字段，在model类中用@property声明，默认就是read_only

    ```python
    @property
    def gender(self):
    	return self.get_sex_display()
    ```

  - 4: 自定义只写字段，在serializer类中声明，必须手动明确write_only

    ```python
    re_password = serializers.CharField(write_only=True)
    ```

  - 特殊）在serializer类中声明，没有明确write_only，是对model原有字段的覆盖，且可读可写
    password = serializers.CharField()

  - 5: 用 extra_kwargs 来为 写字段 制定基础校验规则（了解）

  - 6: 每一个 写字段 都可以用局部钩子 validate_字段(self, value) 方法来自定义校验规则，成功返回value，失败抛出 exceptions.ValidationError('异常信息') 异常

  - 7: 需要联合校验的 写字段们，用 validate(self, attrs) 方法来自定义校验规则，，成功返回attrs，失败抛出 exceptions.ValidationError({'异常字段': '异常信息'}) 异常

  - 8: extra_kwargs中一些重要的限制条件
    	i）'required'：代表是否必须参与写操作，有默认值或可以为空的字段，该值为False；反之该值为True；也可以手动修改值

###### 序列化类其他配置

```python
class AuthorModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Author
        # 不常用，将全部字段提供给外界
        fields = '__all__' 
        
# ------------------------------------------------------------------

class AuthorModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Author
        # 不常用，排除指定字段的其他所有字段，不能自动包含 外键反向 字段
        exclude = ['is_delete', 'updated_time']  
        
# ------------------------------------------------------------------

class AuthorModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Author
        # 'detail', 'books' 是 外键(正向|反向) 字段
        fields = ['name', 'detail', 'books']
        # 不常用，自动深度，自动深度会显示外键关联表的所有字段
        depth = 2  
# 正向外键字段：就是外键的属性名
# 反向外键字段：就是外键属性设置的related_name
```

###### 子序列化

```python
"""
1）子序列化的字段，必须是 外键(正向|反向) 字段
2）子序列化对应的数据是单个many=False，数据对应是多个many=True
3）子序列化其实就是自定义序列化字段，覆盖了原有 外键(正向|反向)字段 的规则，所以不能进行反序列化
"""
```

- `url.py`

  ```python
  url(r'^authors/$', views.AuthorAPIView.as_view()),
  url(r'^authors/(?P<pk>\d+)/$', views.AuthorAPIView.as_view()),
  ```

- `serializers.py`

  ```python
  from rest_framework import serializers
  from . import models
  class AuthorDetailModelSerializer(serializers.ModelSerializer):
      class Meta:
          model = models.AuthorDetail
          fields = ['phone']
  
  class BookModelSerializer(serializers.ModelSerializer):
      class Meta:
          model = models.Book
          fields = ['name', 'price']
  
  class AuthorModelSerializer(serializers.ModelSerializer):
      # 子序列化：子序列化类必须写在上方，且只能对 外键(正向反向)字段 进行覆盖
      # 注：运用了子序列化的外键字段，就不能进行数据库的反序列化过程
      detail = AuthorDetailModelSerializer(many=False, read_only=True)
      books = BookModelSerializer(many=True, read_only=True)
      # 问题：
      # 1）不设置read_only时，就相当于允许反序列化，反序列化是就会报错
      # 2）设置read_only时，可以完成反序列化，但是新增的数据再序列化了，就没有外键关联的数据，与原来数据格式就不一致了
      class Meta:
          model = models.Author
          fields = ['name', 'detail', 'books']
  ```

- `views.py`

  ```python
  # 实际开发，资源的大量操作都是查询操作，只有查需求的资源，可以采用子序列化
  class AuthorAPIView(APIView):
      def get(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          if pk:
              obj = models.Author.objects.filter(is_delete=False, pk=pk).first()
              serializer = serializers.AuthorModelSerializer(instance=obj)
              return APIResponse(result=serializer.data)
          else:
              queryset = models.Author.objects.filter(is_delete=False).all()
              serializer = serializers.AuthorModelSerializer(instance=queryset, many=True)
              return APIResponse(results=serializer.data)
  
      # 测试子序列化外键字段，不能参与反序列化，因为
      def post(self, request, *args, **kwargs):
          serializer = serializers.AuthorModelSerializer(data=request.data)
          if serializer.is_valid():
              obj = serializer.save()
              return APIResponse(result=serializers.AuthorModelSerializer(instance=obj).data, http_status=201)
          else:
              # 校验失败 => 异常响应
              return APIResponse(1, serializer.errors, http_status=400)
  ```

###### 多表序列化与反序列化

```python
"""
1）外键字段要参与反序列化，所以外键字段设置为write_only
2）外键关系需要连表序列化结果给前台，可以用@property来自定义连表序列化
"""
```

- `urls.py`

  ```python
  url(r'^books/$', views.BookAPIView.as_view()),
  url(r'^books/(?P<pk>\d+)/$', views.BookAPIView.as_view()),
  ```

- `models.py`

  ```python
  class Book(BaseModel):
      # ...
      
      @property  # @property字段默认就是read_only，且不允许修改
      def publish_name(self):
          return self.publish.name
  
      @property  # 自定义序列化过程
      def author_list(self):
          temp_author_list = []
          for author in self.authors.all():
              author_dic = {
                  "name": author.name
              }
              try:
                  author_dic['phone'] = author.detail.phone
              except:
                  author_dic['phone'] = ''
              temp_author_list.append(author_dic)
          return temp_author_list
  
      @property  # 借助序列化类完成序列化过程
      def read_author_list(self):
          from .serializers import AuthorModelSerializer
          return AuthorModelSerializer(self.authors.all(), many=True).data
  ```

- `serializers`

  ```python
  # 辅助序列化类
  class AuthorDetailModelSerializer(serializers.ModelSerializer):
      class Meta:
          model = models.AuthorDetail
          fields = ['phone']
  
  # 辅助序列化类
  class AuthorModelSerializer(serializers.ModelSerializer):
      detail = AuthorDetailModelSerializer(many=False, read_only=True)
      class Meta:
          model = models.Author
          fields = ['name', 'detail']
  
  
  # 主序列化类
  class BookModelSerializer(serializers.ModelSerializer):
      class Meta:
          model = models.Book
          fields = ('name', 'price', 'image', 'publish', 'authors', 'publish_name', 'author_list', 'read_author_list')
          extra_kwargs = {
              'image': {
                  'read_only': True,
              },
              'publish': {  # 系统原有的外键字段，要留给反序列化过程使用，序列化外键内容，用@property自定义
                  'write_only': True,
              },
              'authors': {
                  'write_only': True,
              }
          }
  ```

- `views.py`

  ```python
  # 六个必备接口：单查、群查、单增、单删、单整体改(了解)、单局部改
  # 四个额外接口：群增、群删、群整体改、群局部改
  class BookAPIView(APIView):
      # 单查群查
      def get(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          if pk:
              obj = models.Book.objects.filter(is_delete=False, pk=pk).first()
              serializer = serializers.BookModelSerializer(instance=obj)
              return APIResponse(result=serializer.data)
          else:
              queryset = models.Book.objects.filter(is_delete=False).all()
              serializer = serializers.BookModelSerializer(instance=queryset, many=True)
              return APIResponse(results=serializer.data)
  
  
      # 单增群增
      def post(self, request, *args, **kwargs):
          # 如何区别单增群增：request.data是{}还是[]
          if not isinstance(request.data, list):
              # 单增
              serializer = serializers.BookModelSerializer(data=request.data)
              serializer.is_valid(raise_exception=True)  # 如果校验失败，会直接抛异常，返回给前台
              obj = serializer.save()
              # 为什么要将新增的对象重新序列化给前台：序列化与反序列化数据不对等
              return APIResponse(result=serializers.BookModelSerializer(obj).data, http_status=201)
          else:
              # 群增
              serializer = serializers.BookModelSerializer(data=request.data, many=True)
              serializer.is_valid(raise_exception=True)  # 如果校验失败，会直接抛异常，返回给前台
              objs = serializer.save()
              # 为什么要将新增的对象重新序列化给前台：序列化与反序列化数据不对等
              return APIResponse(result=serializers.BookModelSerializer(objs, many=True).data, http_status=201)
  
  # 友情注释：群增其实是借助了ListSerializer来的create方法完成的
  ```

###### 序列化类外键字段的覆盖

```python
"""
1）在序列化类中自定义字段，名字与model类中属性名一致，就称之为覆盖操作
	（覆盖的是属性的所有规则：extra_kwargs中指定的简易规则、model字段提供的默认规则、数据库唯一约束等哪些规则）

2）外键覆盖字段用PrimaryKeyRelatedField来实现，可以做到只读、只写、可读可写三种形式
	只读：read_only=True
	只写：queryset=关联表的queryset, write_only=True
	可读可写：queryset=关联表的queryset
	
3）当外界关联的数据是多个时，需标识many=True条件
"""
```

- `serializers.py`

  ```python
  class BookModelSerializer(serializers.ModelSerializer):
      # 如何覆盖外键字段
      # publish = serializers.PrimaryKeyRelatedField(read_only=True)  # 只读
      # publish = serializers.PrimaryKeyRelatedField(queryset=models.Publish.objects.all(), write_only=True)  # 只写
      # publish = serializers.PrimaryKeyRelatedField(queryset=models.Publish.objects.all())  # 可读可写
  
      publish = serializers.PrimaryKeyRelatedField(queryset=models.Publish.objects.all())
      authors = serializers.PrimaryKeyRelatedField(queryset=models.Author.objects.all(), many=True)
  
      class Meta:
          model = models.Book
          fields = ('name', 'price', 'image', 'publish', 'authors')
  ```

###### 十大接口 序列化总结

```python
"""
1）初始化序列化类，设置partial=True可以将所有反序列化字段的 required 设置为 False（提供就校验，不提供不校验），可以运用在局部修改接口

2）初始化序列化类，设置context={...}，在序列化类操作self.context，实现视图类和序列化类数据互通

3）只有要完成资源的群改这种特殊需求时，才需要自定义ListSerializer绑定给自定义的ModelSerializer，重写update方法，来完成需求
"""
```



```python
"""
def __init__(self, instance=None, data=empty, **kwargs):
    pass

instance：是要被赋值对象的 - 对象类型数据赋值给instance
data：是要被赋值数据的 - 请求来的数据赋值给data
kwargs：内部有三个属性：many、partial、context
    many：操作的对象或数据，是单个的还是多个的
    partial：在修改需求时使用，可以将所有校验字段required校验规则设置为False
    context：用于视图类和序列化类直接传参使用
"""

# 常见使用
# 单查接口
UserModelSerializer(instance=user_obj)

# 群查接口
UserModelSerializer(instance=user_query, many=True)

# 单增接口，request.data是字典
UserModelSerializer(data=request.data) 

# 群增接口，request.data是列表
UserModelSerializer(data=request.data, many=True)

# 单整体改接口，request.data是字典
UserModelSerializer(instance=user_obj, data=request.data)

# 群整体改接口，request.data是列表，且可以分离出pks，转换成user_queryset
UserModelSerializer(instance=user_queryset, data=request.data, many=True)

# 单局部改接口，request.data是字典
UserModelSerializer(instance=user_obj, data=request.data, partial=True)

# 群局部改接口，request.data是列表，且可以分离出pks，转换成user_queryset
UserModelSerializer(instance=user_queryset, data=request.data, partial=True, many=True)

# 删接口，用不到序列化类
```

- `models.py`

  ```python
  
  # 基类：是抽象的(不会完成数据库迁移)，目的是提供共有字段的
  class BaseModel(models.Model):
      is_delete = models.BooleanField(default=False)
      updated_time = models.DateTimeField(auto_now_add=True)
  
      class Meta:
          abstract = True  # 必须完成该配置
  
  class Book(BaseModel):
      name = models.CharField(max_length=64)
      price = models.DecimalField(max_digits=5, decimal_places=2, null=True)
      image = models.ImageField(upload_to='img', default='img/default.png')
  
      publish = models.ForeignKey(to='Publish', related_name='books', db_constraint=False, on_delete=models.DO_NOTHING)
      authors = models.ManyToManyField(to='Author', related_name='books', db_constraint=False)
  	
      @property  # @property字段默认就是read_only，且不允许修改
      def publish_name(self):
          return self.publish.name
  
      @property  # 自定义序列化过程
      def author_list(self):
          temp_author_list = []
          for author in self.authors.all():
              author_dic = {
                  "name": author.name
              }
              try:
                  author_dic['phone'] = author.detail.phone
              except:
                  author_dic['phone'] = ''
              temp_author_list.append(author_dic)
          return temp_author_list
      
  
  class Publish(BaseModel):
      name = models.CharField(max_length=64)
  
  
  class Author(BaseModel):
      name = models.CharField(max_length=64)
  
  
  class AuthorDetail(BaseModel):
      phone = models.CharField(max_length=11)
      author = models.OneToOneField(to=Author, related_name='detail', db_constraint=False, on_delete=models.CASCADE)
     
  ```

- `urls.py`

  ```python
  
  url(r'^books/$', views.BookAPIView.as_view()),
  url(r'^books/(?P<pk>\d+)/$', views.BookAPIView.as_view()),
  ```

- `serializers.py`

  ```python
  from rest_framework import serializers
  from . import models
  
  # 群增群改辅助类（了解）
  class BookListSerializer(serializers.ListSerializer):
      """
      1）create群增方法不需要重新
      2）update群改方法需要重写，且需要和views中处理request.data的逻辑配套使用
      3）self.child就代表该ListSerializer类绑定的ModelSerializer类
          BookListSerializer的self.child就是BookModelSerializer
      """
      # 重新update方法
      def update(self, queryset, validated_data_list):
          return [
              self.child.update(queryset[index], validated_data) for index, validated_data in enumerate(validated_data_list)
          ]
  
  # 主序列化类
  class BookModelSerializer(serializers.ModelSerializer):
      class Meta:
          # 配置自定义群增群改序列化类
          list_serializer_class = BookListSerializer
  
          model = models.Book
          fields = ('name', 'price', 'image', 'publish', 'authors', 'publish_name', 'author_list')
          # fields = ('name', 'price', 'image', 'publish', 'authors', 'abc')
          extra_kwargs = {
              'image': {
                  'read_only': True,
              },
              'publish': {  # 系统原有的外键字段，要留给反序列化过程使用，序列化外键内容，用@property自定义
                  'write_only': True,
              },
              'authors': {
                  'write_only': True,
              },
          }
  
      # 需求：内外传参
      # 1）在钩子函数中，获得请求请求对象request 视图类 => 序列化类
      # 2）序列化钩子校验过程中，也会产生一些数据，这些数据能不能传递给外界使用：序列化类 => 视图类
      # 序列化类的context属性，被视图类与序列化类共享
      def validate(self, attrs):
          print(self.context)  # 可以获得视图类在初始化序列化对象时传入的context
          # self.context.update({'a': 10})  # 序列化类内部更新context，传递给视图类
          return attrs
  
  
  
  ```

- `views.py`

  ```python
  from rest_framework.views import APIView
  from . import models, serializers
  from .response import APIResponse
  
  # 六个必备接口：单查、群查、单增、单删、单整体改(了解)、单局部改
  # 四个额外接口：群增、群删、群整体改、群局部改
  class BookAPIView(APIView):
      # 单查群查
      """
      单查：接口：/books/(pk)/
      群查：接口：/books/
      """
      def get(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          if pk:
              obj = models.Book.objects.filter(is_delete=False, pk=pk).first()
              serializer = serializers.BookModelSerializer(instance=obj)
              return APIResponse(result=serializer.data)
          else:
              queryset = models.Book.objects.filter(is_delete=False).all()
              serializer = serializers.BookModelSerializer(instance=queryset, many=True)
              return APIResponse(results=serializer.data)
  
      # 单增群增
      """
      单增：接口：/books/   数据：dict
      群增：接口：/books/   数据：list
      """
      def post(self, request, *args, **kwargs):
          # 如何区别单增群增：request.data是{}还是[]
          if not isinstance(request.data, list):
              # 单增
              serializer = serializers.BookModelSerializer(data=request.data)
              serializer.is_valid(raise_exception=True)  # 如果校验失败，会直接抛异常，返回给前台
              obj = serializer.save()
              # 为什么要将新增的对象重新序列化给前台：序列化与反序列化数据不对等
              return APIResponse(result=serializers.BookModelSerializer(obj).data, http_status=201)
          else:
              # 群增
              serializer = serializers.BookModelSerializer(data=request.data, many=True)
              serializer.is_valid(raise_exception=True)  # 如果校验失败，会直接抛异常，返回给前台
              objs = serializer.save()
              # 为什么要将新增的对象重新序列化给前台：序列化与反序列化数据不对等
              return APIResponse(result=serializers.BookModelSerializer(objs, many=True).data, http_status=201)
  
      # 单删群删
      """
      单删：接口：/books/(pk)/
      群删：接口：/books/   数据：[pk1, ..., pkn]
      """
      def delete(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          if pk:
              pks = [pk]  # 将单删伪装成群删一条
          else:
              pks = request.data  # 群删的数据就是群删的主键们
  
          try:  # request.data可能提交的是乱七八糟的数据，所以orm操作可能会异常
              rows = models.Book.objects.filter(is_delete=False, pk__in=pks).update(is_delete=True)
          except:
              return APIResponse(1, '数据有误')
  
          if rows:  # 只要有受影响的行，就代表删除成功
              return APIResponse(0, '删除成功')
  
          return APIResponse(2, '删除失败')
  
      # 单整体改群整体改
      """
      单整体改：接口：/books/(pk)/ 数据：dict
      群整体改：接口：/books/   数据：[{pk1, ...}, ..., {pkn, ...}] | {pks: [pk1, ..., pkn], data: [{}, ..., {}]}
      """
      def put(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          if pk:  # 单
              try:
                  instance = models.Book.objects.get(is_delete=False, pk=pk)
              except:
                  return APIResponse(1, 'pk error', http_status=400)
              # 序列化类同时赋值instance和data，代表用data重新更新instance => 修改
              serializer = serializers.BookModelSerializer(instance=instance, data=request.data)
              serializer.is_valid(raise_exception=True)
              obj = serializer.save()
              return APIResponse(result=serializers.BookModelSerializer(obj).data)
          else:  # 群
              """ 分析request.data数据 [{'pk':1, 'name': '', 'publish': 1, 'authors': [1, 2]}, ...]
              1）从 request.data 中分离出 pks 列表
              2）pks中存放的pk在数据库中没有对应数据，或者对应的数据已经被删除了，这些不合理的pk要被剔除
              3）pks最终转换得到的 objs 列表长度与 request.data 列表长度不一致，就是数据有误
              """
              pks = []
              try:  # 只要不是要求的标准数据，一定会在下方四行代码某一行抛出异常
                  for dic in request.data:
                      pks.append(dic.pop('pk'))
                  objs = models.Book.objects.filter(is_delete=False, pk__in=pks)
                  assert len(objs) == len(request.data)  # 两个列表长度必须一致
              except:
                  return APIResponse(1, '数据有误', http_status=400)
  
              serializer = serializers.BookModelSerializer(instance=objs, data=request.data, many=True)
              serializer.is_valid(raise_exception=True)
              objs = serializer.save()
              return APIResponse(result=serializers.BookModelSerializer(objs, many=True).data)
  
  
      # 单局部改群局部改
      """
      单局部改：接口：/books/(pk)/ 数据：dict
      群局部改：接口：/books/   数据：[{pk1, ...}, ..., {pkn, ...}] | {pks: [pk1, ..., pkn], data: [{}, ..., {}]}
      """
      def patch(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          if pk:  # 单
              try:
                  instance = models.Book.objects.get(is_delete=False, pk=pk)
              except:
                  return APIResponse(1, 'pk error', http_status=400)
              # partial=True就是将所有反序列化字段的 required 设置为 False（提供就校验，不提供不校验）
              serializer = serializers.BookModelSerializer(instance=instance, data=request.data, partial=True)
              serializer.is_valid(raise_exception=True)
              obj = serializer.save()
              return APIResponse(result=serializers.BookModelSerializer(obj).data)
          else:  # 群
              pks = []
              try:  # 只要不是要求的标准数据，一定会在下方三行代码某一行抛出异常
                  for dic in request.data:
                      pks.append(dic.get('pk'))
                  objs = models.Book.objects.filter(is_delete=False, pk__in=pks)
                  assert len(objs) == len(request.data)  # 两个列表长度必须一致
              except:
                  return APIResponse(1, '数据有误', http_status=400)
  
              serializer = serializers.BookModelSerializer(
                  instance=objs,
                  data=request.data,
                  many=True,
                  partial=True,
                  context={'request': request}  # 初始化时，对context赋值，将视图类中数据传递给序列化类
              )
  
              serializer.is_valid(raise_exception=True)
              objs = serializer.save()
  
              print(serializer.context)  # 在完成序列化类校验后，可以重新拿到序列化类内部对context做的值更新
              return APIResponse(result=serializers.BookModelSerializer(objs, many=True).data)
  ```

  









###### 开发流程

```python
1）在model类中自定义 读字段，在serializer类中自定义 写字段
2）将model自带字段和所以自定义字段书写在fields中，用write_only和read_only区别model自带字段
3）可以写基础校验规则，也可以省略
4）制定局部及全局钩子
```

##### Response

######  二次封装Response

```python
# 新建response.py文件
from rest_framework.response import Response

class APIResponse(Response):
    def __init__(self, status=0, msg='ok', http_status=None, headers=None, exception=False, **kwargs):
        # 将外界传入的数据状态码、状态信息以及其他所有额外存储在kwargs中的信息，都格式化成data数据
        data = {
            'status': status,
            'msg': msg
        }
        # 在外界数据可以用result和results来存储
        if kwargs:
            data.update(kwargs)

        super().__init__(data=data, status=http_status, headers=headers, exception=exception)


# 使用：
# APIResponse() 代表就返回 {"status": 0, "msg": "ok"}

# APIResponse(result="结果") 代表返回 {"status": 0, "msg": "ok", "result": "结果"}

# APIResponse(status=1, msg='error', http_status=400, exception=True) 异常返回 {"status": 1, "msg": "error"}
```



##### 视图家族

```python
"""
视图基类：APIView、GenericAPIView
视图工具类：mixins包下的五个类（六个方法）
工具视图类：generics包下的所有GenericAPIView的子类
视图集：viewsets包下的类
"""

""" GenericAPIView基类（基本不会单独使用，了解即可，但是是高级视图类的依赖基础）
1）GenericAPIView继承APIV iew，所有APIView子类写法在继承GenericAPIView时可以保持一致
2）GenericAPIView给我们提供了三个属性 queryset、serializer_class、lookup_field
3）GenericAPIView给我们提供了三个方法 get_queryset、get_serializer、get_obj
"""


""" mixins包存放了视图工具类（不能单独使用，必须配合GenericAPIView使用）
CreateModelMixin：单增工具类
	create方法
	
ListModelMixin：群查工具类
	list方法

RetrieveModelMixin：单查工具类
	retrieve方法

UpdateModelMixin：单整体局部改工具类
	update方法

DestroyModelMixin：单删工具类
	destory方法
"""

""" generics包下的所有GenericAPIView的子类（就是继承GenericAPIView和不同mixins下的工具类的组合）
1）定义的视图类，继承generics包下已有的特点的GenericAPIView子类，可以在只初始化queryset和serializer_class两个类属性后，就获得特定的功能

2）定义的视图类，自己手动继承GenericAPIView基类，再任意组合mixins包下的一个或多个工具类，可以实现自定义的工具视图类，获得特定的功能或功能们

注：
i）在这些模式下，不能实现单查群查共存（可以加逻辑区分，也可以用视图集知识）
ii）DestroyModelMixin工具类提供的destory方法默认是从数据库中删除数据，所以一般删除数据的需求需要自定义逻辑
"""
```



###### views.py

```python
# ----------------------------- 过渡写法：了解 -----------------------------

from rest_framework.generics import GenericAPIView
class BookV1APIView(GenericAPIView):
    # 将数据和序列化提示为类属性，所有的请求方法都可以复用
    queryset = models.Book.objects.filter(is_delete=False).all()
    serializer_class = serializers.BookModelSerializer
    lookup_field = 'pk'  # 可以省略，默认是pk，与url有名分组对应的

    # 群查
    def get(self, request, *args, **kwargs):
        # queryset = models.Book.objects.filter(is_delete=False).all()  # => 方法+属性两行代码
        queryset = self.get_queryset()
        # serializer = serializers.BookModelSerializer(instance=queryset, many=True)  # => 方法+属性两行代码
        serializer = self.get_serializer(instance=queryset, many=True)
        return APIResponse(results=serializer.data)

    # 单查
    # def get(self, request, *args, **kwargs):
    #     obj = self.get_object()
    #     serializer = self.get_serializer(obj)
    #     return APIResponse(results=serializer.data)

    # 单增
    def post(self, request, *args, **kwargs):
        # serializer = serializers.BookModelSerializer(data=request.data)
        serializer = self.get_serializer(data=request.data)  # 同样的步骤多了，好处就来了
        serializer.is_valid(raise_exception=True)
        obj = serializer.save()
        return APIResponse(result=self.get_serializer(obj).data, http_status=201)
    
# ----------------------------- 过渡写法：了解 -----------------------------

from rest_framework.generics import GenericAPIView
from rest_framework import mixins
class BookV2APIView(GenericAPIView, mixins.RetrieveModelMixin, mixins.CreateModelMixin):
    queryset = models.Book.objects.filter(is_delete=False).all()
    serializer_class = serializers.BookModelSerializer

    # 单查
    def get(self, request, *args, **kwargs):
        # obj = self.get_object()
        # serializer = self.get_serializer(obj)
        # return APIResponse(results=serializer.data)

        # return self.retrieve(request, *args, **kwargs)

        response = self.retrieve(request, *args, **kwargs)
        return APIResponse(result=response.data)

    # 单增
    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)


# ----------------------------- 开发写法：常用 -----------------------------

from rest_framework.generics import RetrieveAPIView
class BookV3APIView(RetrieveAPIView):
    queryset = models.Book.objects.filter(is_delete=False).all()
    serializer_class = serializers.BookModelSerializer

    # 单查
    pass
```

###### `urls.py`

```python
from django.conf.urls import url
from . import views

urlpatterns = [
    # ...
    
    url(r'^v1/books/$', views.BookV1APIView.as_view()),
    url(r'^v1/books/(?P<pk>\d+)/$', views.BookV1APIView.as_view()),

    url(r'^v2/books/$', views.BookV2APIView.as_view()),
    url(r'^v2/books/(?P<pk>\d+)/$', views.BookV2APIView.as_view()),

    url(r'^v3/books/$', views.BookV3APIView.as_view()),
    url(r'^v3/books/(?P<pk>\d+)/$', views.BookV3APIView.as_view()),
]

```

###### 基于generics包下工具视图类的六大基础接口

- `views.py`

  ```python
  # 六大基础接口
  # 1）直接继承generics包下的工具视图类，可以完成六大基础接口
  # 2）单查群查不能共存
  # 3）单删一般会重写
  ```

  ```python
  from . import models, serializers
  from rest_framework.response import Response
  from rest_framework import generics
  class BookV2APIView(generics.ListAPIView,
                      generics.RetrieveAPIView,
                      generics.CreateAPIView,
                      generics.UpdateAPIView,
                      generics.DestroyAPIView):
      queryset = models.Book.objects.filter(is_delete=False).all()
      serializer_class = serializers.BookModelSerializer
  
      def get(self, request, *args, **kwargs):
          if 'pk' in kwargs:
              return self.retrieve(request, *args, **kwargs)
          return self.list(request, *args, **kwargs)
  
      def delete(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          models.Book.objects.filter(is_delete=False, pk=pk).update(is_delete=True)
          return Response(status=204)
  ```

###### 视图集

- 导入

  ```python
  """ ViewSetMixin类存在理由推到
  1）工具视图类，可以完全应付六大基础接口，唯一缺点是单查与群查接口无法共存
  	（只配置queryset、serializer_class、lookup_field）
  2）不能共存的原因：RetrieveAPIView和ListAPIView都是get方法，不管带不带pk的get请求，只能映射给一个get方法
  3）能不能修改映射关系：
  	比如将/books/的get请求映射给list方法，
  	将/books/(pk)/的get请求映射给retrieve方法，
  	甚至可以随意自定义映射关系
  """
  ```

- 源码分析

  ```python
   继承视图集的视图类的as_view都是走ViewSetMixin类的，核心源码分析
  @classonlymethod
  def as_view(cls, actions=None, **initkwargs):
      # ...
  
      # 没有actions，也就是调用as_view()没有传参，像as_view({'get': 'list'})
      if not actions:
          raise TypeError("The `actions` argument must be provided when "
                          "calling `.as_view()` on a ViewSet. For example "
                          "`.as_view({'get': 'list'})`")
  
          # ...
  
          # 请求来了走view函数
          def view(request, *args, **kwargs):
              # ...
              # 解析actions，修改 请求分发 - 响应函数 映射关系
              self.action_map = actions
              for method, action in actions.items():  # method:get | action:list
                  handler = getattr(self, action)  # 从我们视图类用action:list去反射，所以handler是list函数，不是get函数
                  setattr(self, method, handler)  # 将get请求对应list函数，所以在dispath分发请求时，会将get请求分发给list函数
  
                  # ...
                  # 通过视图类的dispatch完成最后的请求分发
                  return self.dispatch(request, *args, **kwargs)
  
              # ...
              # 保存actions映射关系，以便后期使用
              view.actions = actions
              return csrf_exempt(view)
  ```

- 核心

  ```python
  """
  视图集的使用总结
  1）可以直接继承ModelViewSet，实现六大继承接口（是否重写destroy方法，或其他方法，根据需求决定）
  2）可以直接继承ReadOnlyModelViewSet，实现只读需求（只有单查群查）
  3）继承ViewSet类，与Model类关系不是很密切的接口：登录的post请求，是查询操作；短信验证码发生接口，借助第三方平台
  4）继承GenericViewSet类，就代表要配合mixins包，自己完成任意组合
  5）继承以上4个视图集任何一个，都可以与路由as_view({映射})配合，完成自定义请求响应方法
  """
  ```

- 案例

  `urls.py`

  ```python
  url(r'^v3/books/$', views.BookV3APIView.as_view(
      {'get': 'list', 'post': 'create', 'delete': 'multiple_destroy'}
  	)),
  
  url(r'^v3/books/(?P<pk>\d+)/$', views.BookV3APIView.as_view(
          {'get': 'retrieve', 'put': 'update', 'patch': 'partial_update', 'delete': 'destroy'}
      )),
  ```

  `views.py`

  ```python
  from rest_framework.viewsets import ModelViewSet
  from rest_framework.response import Response
  class BookV3APIView(ModelViewSet):
      queryset = models.Book.objects.filter(is_delete=False).all()
      serializer_class = serializers.BookModelSerializer
  
      # 可以在urls.py中as_view({'get': 'my_list'})自定义请求映射
      def my_list(self, request, *args, **kwargs):
          return Response('ok')
  
      # 需要完成字段删除，不是重写delete方法，而是重写destroy方法
      def destroy(self, request, *args, **kwargs):
          pk = kwargs.get('pk')
          models.Book.objects.filter(is_delete=False, pk=pk).update(is_delete=True)
          return Response(status=204)
  
  
      # 群删接口
      def multiple_destroy(self, request, *args, **kwargs):
          try:
              models.Book.objects.filter(is_delete=False, pk__in=request.data).update(is_delete=True)
          except:
              return Response(status=400)
          return Response(status=204)
  ```

##### 路由组件 : 必须配合视图集使用

`urls.py`

```python
from django.conf.urls import url, include
from . import views
# 路由组件，必须配合视图集使用
from rest_framework.routers import SimpleRouter
router = SimpleRouter()

# 以后就写视图集的注册即可：BookV3APIView和BookV4APIView都是视图集
router.register('v3/books', views.BookV3APIView, 'book')
router.register('v4/books', views.BookV4APIView, 'book')

urlpatterns = [
    url('', include(router.urls))
]
```

`views.py`

```python
from rest_framework.viewsets import ReadOnlyModelViewSet
class BookV4APIView(ReadOnlyModelViewSet):
    queryset = models.Book.objects.filter(is_delete=False).all()
    serializer_class = serializers.BookModelSerializer
```

######  自定义路由组件

- `router.py`

  ```python
  from rest_framework.routers import SimpleRouter as DrfSimpleRouter
  from rest_framework.routers import Route, DynamicRoute
  
  class SimpleRouter(DrfSimpleRouter):
      routes = [
          # List route.
          Route(
              url=r'^{prefix}{trailing_slash}$',
              mapping={
                  'get': 'list',
                  'post': 'create',  # 注：群增只能自己在视图类中重写create方法，完成区分
                  'delete': 'multiple_destroy',  # 新增：群删
                  'put': 'multiple_update',  # 新增：群整体改
                  'patch': 'multiple_partial_update'  # 新增：群局部改
              },
              name='{basename}-list',
              detail=False,
              initkwargs={'suffix': 'List'}
          ),
          # Dynamically generated list routes. Generated using
          # @action(detail=False) decorator on methods of the viewset.
          DynamicRoute(
              url=r'^{prefix}/{url_path}{trailing_slash}$',
              name='{basename}-{url_name}',
              detail=False,
              initkwargs={}
          ),
          # Detail route.
          Route(
              url=r'^{prefix}/{lookup}{trailing_slash}$',
              mapping={
                  'get': 'retrieve',
                  'put': 'update',
                  'patch': 'partial_update',
                  'delete': 'destroy'
              },
              name='{basename}-detail',
              detail=True,
              initkwargs={'suffix': 'Instance'}
          ),
          # Dynamically generated detail routes. Generated using
          # @action(detail=True) decorator on methods of the viewset.
          DynamicRoute(
              url=r'^{prefix}/{lookup}/{url_path}{trailing_slash}$',
              name='{basename}-{url_name}',
              detail=True,
              initkwargs={}
          ),
      ]
  ```

##### 上传文件接口

###### urls.py

```python
from django.conf.urls import url, include
from . import views
# 路由组件，必须配合视图集使用
from rest_framework.routers import SimpleRouter
router = SimpleRouter()

# /books/image/(pk) 提交 form-data：用image携带图片
router.register('books/image', views.BookUpdateImageAPIView, 'book')

urlpatterns = [
    url('', include(router.urls))
]
```

###### `serializers.py`

```python
class BookUpdateImageModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Book
        fields = ['image']
```

###### `views.py`

```python
# 上次文件 - 修改头像 - 修改海报
from rest_framework.viewsets import GenericViewSet
from rest_framework import mixins
class BookUpdateImageAPIView(GenericViewSet, mixins.UpdateModelMixin):
    queryset = models.Book.objects.filter(is_delete=False).all()
    serializer_class = serializers.BookUpdateImageModelSerializer
```

### 三大认证组件

#### 权限

##### Django权限六表: 扩展User表

##### `models.py`

```python
from django.db import models

# RBAC - Role-Based Access Control
# Django的 Auth组件 采用的认证规则就是RBAC

from django.contrib.auth.models import AbstractUser
class User(AbstractUser):
    mobile = models.CharField(max_length=11, unique=True)

    def __str__(self):
        return self.username


class Book(models.Model):
    name = models.CharField(max_length=64)

    def __str__(self):
        return self.name


class Car(models.Model):
    name = models.CharField(max_length=64)

    def __str__(self):
        return self.name
```

##### `settings.py`

```python
# 自定义User表，要配置
AUTH_USER_MODEL = 'api.User'
```

##### `admin.py`

```python
from django.contrib import admin
from . import models

from django.contrib.auth.admin import UserAdmin as DjangoUserAdmin

# 自定义User表后，admin界面管理User类
class UserAdmin(DjangoUserAdmin):
    # 添加用户课操作字段
    add_fieldsets = (
        (None, {
            'classes': ('wide',),
            'fields': ('username', 'password1', 'password2', 'is_staff', 'mobile', 'groups', 'user_permissions'),
        }),
    )
    # 展示用户呈现的字段
    list_display = ('username', 'mobile', 'is_staff', 'is_active', 'is_superuser')


admin.site.register(models.User, UserAdmin)
admin.site.register(models.Book)
admin.site.register(models.Car)
```

##### 导入

```python
# 1）像专门做人员权限管理的系统（CRM系统）都是公司内部使用，所以数据量都在10w一下，一般效率要求也不是很高
# 2）用户量极大的常规项目，会分两种用户：前台用户(三大认证) 和 后台用户(RBAC来管理)
# 结论：没有特殊要求的Django项目可以直接采用Auth组件的权限六表，不需要自定义六个表，也不需要断开表关系，但可能需要自定义User表
```

##### 做项目是否要分表管理前后台用户

```python
"""
1）是否需要分表
答案：不需要
理由：前后台用户共存的项目，后台用户量都是很少；做人员管理的项目，基本上都是后台用户；前后台用户量都大的会分两个项目处理

2）用户权限六表是否需要断关联
答案：不需要
理由：前台用户占主导的项目，几乎需求只会和User一个表有关；后台用户占主导的项目，用户量不会太大

3）Django项目有没有必须自定义RBAC六表
答案：不需要
理由：auth组件功能十分强大且健全（验证密码，创建用户等各种功能）；admin、xadmin、jwt、drf-jwt组件都是依赖auth组件的（自定义RBAC六表，插件都需要自定义，成本极高）
"""
```

#### 权限六表：RBAC - Role-Based Access Control

##### 三表

![](https://gitee.com/qilitang/Img/raw/master/img/20200309165936.png)

##### 六表



![](https://gitee.com/qilitang/Img/raw/master/img/promission.png)

##### 三大认证规则

![](https://gitee.com/qilitang/Img/raw/master/img/20200309170127.png)

#### 认证组件

##### 重点

```python
"""
1）认证规则
2）如何自定义认证类
3）我们一般不需要自定义认证类，在settings中全局配置第三方 jwt 认证组件提供的认证类即可
"""
```

##### 自定义认证类

```python
"""
1）自定义认证类，继承 BaseAuthentication 类
2）必须重写 authenticate(self, request) 方法
	没有认证信息，返回None：匿名用户(游客) => 匿名用户request.user也有值，就是"匿名对象(Anonymous)"
	有认证信息，且通过，返回(user, token)：合法用户 => user对象会存到request.user中
	有认证信息，不通过，抛异常：非法用户
"""
```

#### 权限组件

##### 重点

```python
"""
1）权限规则
2）如何自定义权限类
3）我们一般在视图类中局部配置drf提供的权限类，但是也会自定义权限类完成局部配置
"""
```

##### 自定义权限类

```python
"""
1）自定义权限类，继承 BasePermission 类
2）必须重写 has_permission(self, request, view): 方法
	设置权限条件，条件通过，返回True：有权限
	设置权限条件，条件失败，返回False：有权限
	
3）drf提供的权限类：
AllowAny：匿名与合法用户都可以
IsAuthenticated：必须登录，只有合法用户可以
IsAdminUser：必须是admin后台用户
IsAuthenticatedOrReadOnly：匿名只读，合法用户无限制
"""
```

#### jwt 认证示意图

```python
""" jwt优势
1）没有数据库写操作，高效
2）服务器不存token，低耗
3）签发校验都是算法，集群
"""
```

![](https://gitee.com/qilitang/Img/raw/master/img/003213.png)

![](https://gitee.com/qilitang/Img/raw/master/img/00321312.png)

![](https://gitee.com/qilitang/Img/raw/master/img/0032133.png)

![](https://gitee.com/qilitang/Img/raw/master/img/00321314.png)

#### jwt 认证算法: 签发与校验

```python
"""
1）jwt分三段式：头.体.签名 （head.payload.sgin）
2）头和体是可逆加密，让服务器可以反解出user对象；签名是不可逆加密，保证整个token的安全性的
3）头体签名三部分，都是采用json格式的字符串，进行加密，可逆加密一般采用base64算法，不可逆加密一般采用hash(md5)算法
4）头中的内容是基本信息：公司信息、项目组信息、token采用的加密方式信息
{
	"company": "公司信息",
	...
}
5）体中的内容是关键信息：用户主键、用户名、签发时客户端信息(设备号、地址)、过期时间
{
	"user_id": 1,
	...
}
6）签名中的内容时安全信息：头的加密结果 + 体的加密结果 + 服务器不对外公开的安全码 进行md5加密
{
	"head": "头的加密字符串",
	"payload": "体的加密字符串",
	"secret_key": "安全码"
}
"""
```

##### 签发：根据登录请求提交来的 账号 + 密码 + 设备信息 签发 token

```python
"""
1）用基本信息存储json字典，采用base64算法加密得到 头字符串
2）用关键信息存储json字典，采用base64算法加密得到 体字符串
3）用头、体加密字符串再加安全码信息存储json字典，采用hash md5算法加密得到 签名字符串

账号密码就能根据User表得到user对象，形成的三段字符串用 . 拼接成token返回给前台
"""
```

##### 校验：根据客户端带token的请求 反解出 user 对象

```python
"""
1）将token按 . 拆分为三段字符串，第一段 头加密字符串 一般不需要做任何处理
2）第二段 体加密字符串，要反解出用户主键，通过主键从User表中就能得到登录用户，过期时间和设备信息都是安全信息，确保token没过期，且时同一设备来的
3）再用 第一段 + 第二段 + 服务器安全码 不可逆md5加密，与第三段 签名字符串 进行碰撞校验，通过后才能代表第二段校验得到的user对象就是合法的登录用户
"""
```

#### drf项目的jwt认证开发流程（重点）

```python
"""
1）用账号密码访问登录接口，登录接口逻辑中调用 签发token 算法，得到token，返回给客户端，客户端自己存到cookies中

2）校验token的算法应该写在认证类中(在认证类中调用)，全局配置给认证组件，所有视图类请求，都会进行认证校验，所以请求带了token，就会反解出user对象，在视图类中用request.user就能访问登录的用户

注：登录接口需要做 认证 + 权限 两个局部禁用
"""
```

#### drf-jwt框架基本使用

##### 安装

```bash
>: pip install djangorestframework-jwt
```

##### 签发token(登录接口)：视图类已经写好了，配置一下路由就行（urls.py）

```python
# api/urls.py
urlpatterns = [
    # ...
    url('^login/$', ObtainJSONWebToken.as_view()),
]

# Postman请求：/api/login/，提供username和password即可
```

##### 校验token(认证组件)：认证类已经写好了，全局配置一下认证组件就行了（settings.py）

```python
# drf-jwt的配置
import datetime
JWT_AUTH = {
    # 配置过期时间
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=7),
}


# drf配置（把配置放在最下方）
REST_FRAMEWORK = {
    # 自定义三大认证配置类们
    'DEFAULT_AUTHENTICATION_CLASSES': ['rest_framework_jwt.authentication.JSONWebTokenAuthentication'],
    # 'DEFAULT_PERMISSION_CLASSES': [],
    # 'DEFAULT_THROTTLE_CLASSES': [],
}
```

##### 设置 需要登录才能访问的接口测试

```python
from rest_framework.permissions import IsAuthenticated
class UserCenterViewSet(GenericViewSet, mixins.RetrieveModelMixin):
    # 设置必须登录才能访问的权限类
    permission_classes = [IsAuthenticated, ]

    queryset = models.User.objects.filter(is_active=True).all()
    serializer_class = serializers.UserCenterSerializer
```

##### 测试封路认证接口

```python
"""
1）用 {"username": "你的用户", "password": "你的密码"} 访问 /api/login/ 接口等到 token 字符串

2）在请求头用 Authorization 携带 "jwt 登录得到的token" 访问 /api/user/center/1/ 接口访问个人中心
"""
```

##### token 的刷新机制

###### drf-jwt 直接提供了刷新功能

```python
"""
1）运用在像12306这样极少数安全性要求高的网站
2）第一个token由登录签发
3）之后的所有正常逻辑，都需要发送两次请求，第一次是刷新token的请求，第二次是正常逻辑的请求
"""
```

###### `settings.py`

```python
import datetime

JWT_AUTH = {
    # 配置过期时间
    'JWT_EXPIRATION_DELTA': datetime.timedelta(minutes=5),

    # 是否可刷新
    'JWT_ALLOW_REFRESH': True,
    # 刷新过期时间
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=7),
}
```

###### urls.py

```python
from rest_framework_jwt.views import ObtainJSONWebToken, RefreshJSONWebToken
urlpatterns = [
    url('^login/$', ObtainJSONWebToken.as_view()),  # 登录签发token接口
    url('^refresh/$', RefreshJSONWebToken.as_view()),  # 刷新toekn接口
]
```

###### Postman

```python
# 接口：/api/refresh/
# 方法：post
# 数据：{"token": "登录签发的token"}
```

##### 认证组件的使用, 多方式登录

###### `urls.py`

```python
# 自定义登录（重点）：post请求 => 查操作(签发token返回给前台) - 自定义路由映射
url('^user/login/$', views.LoginViewSet.as_view({'post': 'login'})),
```

######  `views.py`

```python

# 重点：自定义login，完成多方式登录
from rest_framework.viewsets import ViewSet
from rest_framework.response import Response
class LoginViewSet(ViewSet):
    # 登录接口，要取消所有的认证与权限规则，也就是要做局部禁用操作（空配置）
    authentication_classes = []
    permission_classes = []

    # 需要和mixins结合使用，继承GenericViewSet，不需要则继承ViewSet
    # 为什么继承视图集，不去继承工具视图或视图基类，因为视图集可以自定义路由映射：
    #       可以做到get映射get，get映射list，还可以做到自定义（灵活）
    def login(self, request, *args, **kwargs):
        serializer = serializers.LoginSerializer(data=request.data, context={'request': request})
        serializer.is_valid(raise_exception=True)
        token = serializer.context.get('token')
        return Response({"token": token})
```

###### serializers.py

```python
from rest_framework_jwt.serializers import jwt_payload_handler, jwt_encode_handler

# 重点：自定义login，完成多方式登录
class LoginSerializer(serializers.ModelSerializer):
    # 登录请求，走的是post方法，默认post方法完成的是create入库校验，所以唯一约束的字段，会进行数据库唯一校验，导致逻辑相悖
    # 需要覆盖系统字段，自定义校验规则，就可以避免完成多余的不必要校验，如唯一字段校验
    username = serializers.CharField()
    class Meta:
        model = models.User
        # 结合前台登录布局：采用账号密码登录，或手机密码登录，布局一致，所以不管账号还是手机号，都用username字段提交的
        fields = ('username', 'password')

    def validate(self, attrs):
        # 在全局钩子中，才能提供提供的所需数据，整体校验得到user
        # 再就可以调用签发token算法(drf-jwt框架提供的)，将user信息转换为token
        # 将token存放到context属性中，传给外键视图类使用
        user = self._get_user(attrs)
        payload = jwt_payload_handler(user)
        token = jwt_encode_handler(payload)
        self.context['token'] = token
        return attrs

    # 多方式登录
    def _get_user(self, attrs):
        username = attrs.get('username')
        password = attrs.get('password')
        import re
        if re.match(r'^1[3-9][0-9]{9}$', username):
            # 手机登录
            user = models.User.objects.filter(mobile=username, is_active=True).first()
        elif re.match(r'^.+@.+$', username):
            # 邮箱登录
            user = models.User.objects.filter(email=username, is_active=True).first()
        else:
            # 账号登录
            user = models.User.objects.filter(username=username, is_active=True).first()
        if user and user.check_password(password):
            return user

        raise ValidationError({'user': 'user error'})

```

##### 权限组件的使用: VIP 用户权限

###### 数据准备

```python
"""
1）User表创建两条数据
2）Group表创建一条数据，name叫vip
3）操作User和Group的关系表，让1号用户属于1号vip组
"""
```

###### permissions.py

```python

from rest_framework.permissions import BasePermission

from django.contrib.auth.models import Group
class IsVipUser(BasePermission):
    def has_permission(self, request, view):
        if request.user and request.user.is_authenticated:  # 必须是合法用户
            try:
                vip_group = Group.objects.get(name='vip')
                if vip_group in request.user.groups.all():  # 用户可能不属于任何分组
                    return True  # 必须是vip分组用户
            except:
                pass

        return False
```

###### views.py

```python

from .permissions import IsVipUser
class CarViewSet(ModelViewSet):
    permission_classes = [IsVipUser]

    queryset = models.Car.objects.all()
    serializer_class = serializers.CarSerializer
```

###### serializers.py

```python
class CarSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Car
        fields = ('name', )
```

###### urls.py

```python
router.register('cars', views.CarViewSet, 'car')
```

#### 频率组件

##### 重点

```python
"""
1）如何自定义频率类
2）频率校验的规则
3）自定义频率类是最常见的：短信接口一分钟只能发生一条短信
"""
```

##### 自定义频率组件

```python
"""
1）自定义类继承SimpleRateThrottle
2）设置类实现scope，值就是一个字符串，与settings中的DEFAULT_THROTTLE_RATES进行对应
	DEFAULT_THROTTLE_RATES就是设置scope绑定的类的频率规则：1/min 就代表一分钟只能访问一次
3）重写 get_cache_key(self, request, view) 方法，指定限制条件
	不满足限制条件，返回None：代表对这类请求不进行频率限制
	满足限制条件，返回一个字符串(是动态的)：代表对这类请求进行频率限制
		短信频率限制类，返回 "throttling_%(mobile)s" % {"mobile": 实际请求来的电话}
"""
```

##### 系统频率类

```python
#1）UserRateThrottle: 限制所有用户访问频率
#2）AnonRateThrottle：只限制匿名用户访问频率
```

##### 频率组件项目使用：请求方式频率限制

###### throteles.py

```python
from rest_framework.throttling import SimpleRateThrottle
# 只限制查接口的频率，不限制增删改的频率
class MethodRateThrottle(SimpleRateThrottle):
    scope = 'method'
    def get_cache_key(self, request, view):
        # 只有对get请求进行频率限制
        if request.method.lower() not in ('get', 'head', 'option'):
            return None

        # 区别不同的访问用户，之间的限制是不冲突的
        if request.user.is_authenticated:
            ident = request.user.pk
        else:
            # get_ident是BaseThrottle提供的方法，会根据请求头，区别匿名用户，
            # 保证不同客户端的请求都是代表一个独立的匿名用户
            ident = self.get_ident(request)
        return self.cache_format % {'scope': self.scope, 'ident': ident}
```

###### settings.py

```python
REST_FRAMEWORK = {
    #  ...
    # 频率规则配置
    'DEFAULT_THROTTLE_RATES': {
        # 只能设置 s，m，h，d，且只需要第一个字母匹配就ok，m = min = maaa 就代表分钟
        'user': '3/min',  # 配合drf提供的 UserRateThrottle 使用，限制所有用户访问频率
        'anon': '3/min',  # 配合drf提供的 AnonRateThrottle 使用，只限制匿名用户访问频率
        'method': '3/min',
    },
}
```

###### views.py

```python
from .permissions import IsVipUser
from .throttles import MethodRateThrottle
class CarViewSet(ModelViewSet):
    permission_classes = [IsVipUser]
    throttle_classes = [MethodRateThrottle]

    queryset = models.Car.objects.all()
    serializer_class = serializers.CarSerializer
```

### 异常组件项目使用:记录异常信息到日志文件

#### exception.py

```python
from rest_framework.views import exception_handler as drf_exception_handler
from rest_framework.response import Response
def exception_handler(exc, context):
    # 只处理客户端异常，不处理服务器异常，
    # 如果是客户端异常，response就是可以直接返回给前台的Response对象
    response = drf_exception_handler(exc, context)

    if response is None:
        # 没有处理的服务器异常，处理一下
        # 其实给前台返回 服务器异常 几个字就行了
        # 那我们处理异常模块的目的是 不管任何错误，都有必要进行日志记录（线上项目只能通过记录的日志查看出现过的错误）
        response = Response({'detail': '%s' % exc})

    # 需要结合日志模块进行日志记录的：项目中讲
    return response
```

#### settings.py

```python
REST_FRAMEWORK = {
    # ...
    # 异常模块
    # 'EXCEPTION_HANDLER': 'rest_framework.views.exception_handler',  # 原来的，只处理客户端异常
    'EXCEPTION_HANDLER': 'api.exception.exception_handler',
}
```







### 群查接口相关组件

- 搜索
- 筛选
- 排序
- 分页

**目的**:

- 必须掌握六大接口:
  - 单查
  - 群查
  - 单增
  - 单局部改
  - 整体改
  - 单删

- 十大接口
  - 群增
  - 群局部改
  - 群整体改
  - 群删