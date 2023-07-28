---
layout: post
title: "DRF框架-商城管理"
date: 2023-07-28 11:40
categories: [django, learning]
tags: [django, DRF, b站视频]
typora-root-url: ..
---

## DRF开发RESTful接口
1. 定义模型类  →  models.py
2. 定义序列化器  →  serializers.py
3. 定义路由  →  urls.py
4. 定义视图  →  views.py

## 序列化操作
### 1. 环境准备
1.1. 创建项目
```shell
django-admin startproject DRFdemo1
```
1.2. 创建应用
```shell
python manage.py startapp app1
```
1.3. 修改setting.py中的配置
```python
INSTALLED_APPS = [
    ...
    'app1',
    'rest_framework',
]
```
1.4. app1/models.py中定义模型类
```python
from django.db import models

# Create your models here.

class UserInfo(models.Model):
    # 用户信息模型
    name = models.CharField(max_length=20, verbose_name='用户名')
    pwd = models.CharField(max_length=18, verbose_name='密码')
    email = models.EmailField(max_length=40, verbose_name='邮箱')
    age = models.IntegerField(verbose_name='年龄', default=18)

    def __str__(self):
        return self.name

    class Meta:
        db_table = 'userinfo'
        verbose_name = '用户信息'
```
1.5. app1/serializers.py


