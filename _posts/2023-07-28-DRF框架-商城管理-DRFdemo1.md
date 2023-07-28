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
#### 1.1. 创建项目
```shell
django-admin startproject DRFdemo1
```
#### 1.2. 创建应用
```shell
python manage.py startapp app1
```
#### 1.3. 修改setting.py中的配置
```python
INSTALLED_APPS = [
    ...
    'app1',
    'rest_framework',
]
```
#### 1.4. app1/models.py中定义模型类
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
#### 1.5. app1/serializers.py中定义序列化器类
> 字段约束条件：[序列化程序字段 - Django REST 框架 (django-rest-framework.org)](https://www.django-rest-framework.org/api-guide/fields/)
- 在app1应用中新建文件`serializers.py`
- 在文件`serializers.py`中定义序列化器
```python
from rest_framework import serializers

class UserInfoSerializer(serializers.Serializer):
    # 定义序列化器
    name = serializers.CharField(max_length=10)
    pwd = serializers.CharField(max_length=18)
    email = serializers.EmailField(max_length=20)
    age = serializers.IntegerField(min_value=0, max_value=150)

```
#### 1.6. 激活模型类生成迁移文件，执行迁移文件
```shell
# 生成迁移文件
python manage.py makemigrations
# 执行迁移文件
python manage.py migrate
```
![](/assets/images/2307/Pasted%20image%2020230728153603.png)
#### 1.7. 练习数据准备
```shell
# 打开命令行终端，输入命令
# 进入shell交互环境，或直接打开pycharm中的p
python manage.py shell
# 导入用户模型
from app1.models import UserInfo

# 添加测试数据
userInfo.objects.create(name='张三', pwd='123456zs', email='zs@qq.com', age=19)
userInfo.objects.create(name='李四', pwd='123456ls', email='ls@qq.com', age=19)
userInfo.objects.create(name='王五', pwd='123456ww', email='ww@qq.com', age=21)
```


