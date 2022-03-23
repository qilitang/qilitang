---
title: DRF 框架配置
date: 2019-03-04
comments: false
toc: true
categories: "DRF 框架"
tags: [DRF配置]
---



# DRF 框架安装

## 安装

```python
pip install djangorestframework
```

## 注册

```python
INSTALLED_APPS = [
    # ...
    'rest_framework',
]
```

## 自定义DRF配置

```python
REST_FRAMEWORK = {}

# 注：drf配置查找顺序，自定义settings的REST_FRAMEWORK配置字典 => drf默认settings的DEFAULTS
```

## DRF 封装特点

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.request import Request
```



