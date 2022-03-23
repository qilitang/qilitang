---
title: Django Elasticsearch DSL
date: 2020-09-21
comments: false
toc: true
categories: "Django Elasticserch DSL"
---

# Django Elasticsearch DSL 

## Django Elasticsearch DSL 介绍

Django Elasticsearch DSL是一个软件包，允许在elasticsearch中索引Django模型。它是作为[Elasticsearch-dsl-py](https://github.com/elastic/elasticsearch-dsl-py)的封装而构建的， 因此您可以使用elasticsearch-dsl-py团队开发的所有功能。

您可以在[https://django-elasticsearch-dsl.readthedocs.io上](https://django-elasticsearch-dsl.readthedocs.io/)查看完整文

### 特性

- 基于elasticsearch-dsl-py，因此您可以使用Search类进行查询。

- Django信号接收器处于保存和删除状态，以保持Elasticsearch同步。

- 从Django模型字段中的Elasticsearch自动映射。

- 复杂字段类型支持（ObjectField，NestedField）。

- 使用并行索引快速建立索引。

- Requirements

  - Django >= 1.11
  - Python 2.7, 3.5, 3.6, 3.7

  

Elasticsearch兼容性：该库与5.x以后的所有Elasticsearch版本兼容，但是您必须使用匹配的主要版本：

- 对于Elasticsearch 7.0及更高版本，请使用该库的主要版本7（7.x.y）。

- 对于Elasticsearch 6.0及更高版本，请使用该库的主要版本6（6.x.y）。

- 对于Elasticsearch 5.0和更高版本，请使用库的主要版本0.5（0.5.x）。

  ```python
  # Elasticsearch 7.x
  elasticsearch-dsl>=7.0.0,<8.0.0
  
  # Elasticsearch 6.x
  elasticsearch-dsl>=6.0.0,<7.0.0
  
  # Elasticsearch 5.x
  elasticsearch-dsl>=0.5.1,<6.0.0
  ```

##  快速开始

### 安装和配置

安装 django-elasticsearch-dsl

```python
pip install django-elasticsearch-dsl
```

然后添加 django-elasticsearch-dsl 到 INSTALLED_APPS

```python
INSTALLED_APPS = [
    'django_elasticsearch_dsl',
]

```

必须在django settings 中定义 ELASTICSEARCH_DSL

```python
ELASTICSEARCH_DSL={
    'default': {
        'hosts': 'localhost:9200'
    },
}
```

然后将ELASTICSEARCH_DSL传递到elasticsearch-dsl-py.connections.configure（详细参数请看）。

###　生命要被索引的模型表

model.py

```json
class NcInfo(models.Model):
    cluster = models.CharField(max_length=12, verbose_name='用户名')
    ip = models.GenericIPAddressField(verbose_name='IP地址')
    desc = models.CharField(max_length=128, verbose_name='简介')
    instance_id = models.CharField(max_length=64, verbose_name='实例id')
```

要使该模型与Elasticsearch一起使用，请创建django_elasticsearch_dsl.Document的子类，在Document类中创建一个Index类以定义您的Elasticsearch索引，名称，设置等，最后使用Registry.register_document装饰器注册该类。 它需要在您的应用目录中的documents.py中定义Document类。

创建document.py

```python
from django_elasticsearch_dsl import Document
from django_elasticsearch_dsl.registries import registry
from .models import NcInfo

@registry.register_document
class NcInfoDocument(Document):
    class Index:
        # es 索引名
        name = 'ncinfo'
        # 有关可用设置，请参阅Elasticsearch Indices API参考。
        settings = {'number_of_shards': 1,
                    'number_of_replicas': 0}

    class Django:
        model = NcInfo # 与此文档关联的模型

        # 要在Elasticsearch中建立索引的模型的字段
        fields = [
            'cluster',
            'ip',
            'instance_id',
        ]

        # 保存模型时忽略弹性搜索的自动更新
        # or deleted:
        # ignore_signals = True

        # 每次更新后都不要执行索引刷新（覆盖全局设置）：
        # auto_refresh = False

        # 对django查询集进行分页，该查询集用于以指定大小填充索引
        #（默认情况下，它使用数据库驱动程序的默认设置）
        # queryset_pagination = 5000

```

### 迁移

要创建并填充Elasticsearch索引和映射，请使用search_index命令：



