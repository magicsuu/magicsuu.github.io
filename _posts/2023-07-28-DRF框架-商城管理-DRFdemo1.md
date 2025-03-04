---
layout: post
title: "DRF框架-商城管理"
date: 2023-07-28 11:40
categories: [django, learning]
tags: [django, DRF, b站视频]
typora-root-url: ..
---

# Ⅰ. Django实现RESTful风格：
在开发REST API的视图中，虽然每个视图具体操作的数据不同，但增、删、改、查的实现流程基本套路化，所以这部分代码也是可以复⽤简化编写的：
- 增：校验请求数据 -> 执⾏反序列化过程（将前端传过来的json数据字典转换为模型） -> 保存数据库 -> 将保存的对象序列化并返回
- 删：判断要删除的数据是否存在（使用查询判断） -> 执⾏数据库删除（不涉及序列化与反序列化）
- 改：判断要修改的数据是否存在 -> 校验请求的数据 -> 执⾏反序列化过程 -> 保存数据库 -> 将保存的对象序列化并返回（将模型转换为json数据字典）
- 查：查询数据库 -> 将数据序列化并返回（将模型转换为json字典）
 

# Ⅱ. DRF开发RESTful接口
1. 定义模型类  →  models.py
2. 定义序列化器  →  serializers.py
3. 定义路由  →  urls.py
4. 定义视图  →  views.py

# 一、序列化操作（输出）
- 序列化：将`python对象`转换为`json格式数据`
## 1. 环境准备
### 1.1. 创建项目
```shell
django-admin startproject DRFdemo1
```
### 1.2. 创建应用
```shell
python manage.py startapp app1
```
### 1.3. 修改setting.py中的配置
```python
INSTALLED_APPS = [
    ...
    'app1',
    'rest_framework',
]
```
### 1.4. app1/models.py中定义模型类
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

^8b1c56

### 1.5. app1/serializers.py中定义序列化器类
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

^9cc7bf

### 1.6. 激活模型类生成迁移文件，执行迁移文件
```shell
# 生成迁移文件
python manage.py makemigrations
# 执行迁移文件
python manage.py migrate
```
![](/assets/images/2307/Pasted%20image%2020230728153603.png)
### 1.7. 练习数据准备
```json
# 打开命令行终端，输入命令
# 进入shell交互环境，或直接打开pycharm中的python console控制台
python manage.py shell
# 导入用户模型
from app1.models import UserInfo

# 添加测试数据
UserInfo.objects.create(name='张三', pwd='123456zs', email='zs@qq.com', age=19)
UserInfo.objects.create(name='李四', pwd='123456ls', email='ls@qq.com', age=19)
UserInfo.objects.create(name='王五', pwd='123456ww', email='ww@qq.com', age=21)
```

## 2. 序列化操作
### 2.1. 单个数据序列化
- 进入交互环境
```shell
# 打开pycharm中的python console控制台
# 或
python manage.py shell
```
```python
# 通过模型类查询一条用户信息
# 即python对象obj
>>> from app1.models import UserInfo
>>> obj = UserInfo.objects.get(id=1)
>>> obj
<UserInfo: 张三>

# 使用查询到的用户信息（即python对象obj），创建一个序列化对象ser，
# 将python对象obj传入进去，转换为json格式数据
>>> from app1.serializers import UserInfoSerializer
>>> ser = UserInfoSerializer(obj)
>>> ser.data
{'name': '张三', 'pwd': '123456zs', 'email': 'zs@qq.com', 'age': 19}
```

### 2.2. 查询集序列化
```python
# 对多个数据进行序列化加参数：many=True
>>> objs = UserInfo.objects.all()
>>> objs
<QuerySet [<UserInfo: 张三>, <UserInfo: 李四>, <UserInfo: 王五>]>
>>> sers = UserInfoSerializer(objs, many=True)
>>> sers
UserInfoSerializer(<QuerySet [<UserInfo: 张三>, <UserInfo: 李四>, <UserInfo: 王五>]>, many=True):
    name = CharField(max_length=10)
    pwd = CharField(max_length=18)
    email = EmailField(max_length=20)
    age = IntegerField(max_value=150, min_value=0)
>>> sers.data
[OrderedDict([('name', '张三'), ('pwd', '123456zs'), ('email', 'zs@qq.com'), ('age', 19)]), OrderedDict([('name', '李四'), ('pwd', '123456ls'), ('email', 'ls@qq.com'), ('age', 19)]), OrderedDict([('name', '王五'), ('pwd', '123456ww'), ('email', 'ww@qq.com'), ('age', 21)])]
```

### 2.3. 将序列化得到的数据转换为json
```python
>>> import json
>>> json.dumps(sers.data)
'[{"name": "\\u5f20\\u4e09", "pwd": "123456zs", "email": "zs@qq.com", "age": 19}, {"name": "\\u674e\\u56db", "pwd": "123456ls", "email": "ls@qq.com", "age": 19}, {"name": "\\u738b\\u4e94", "pwd": "123456ww", "email": "ww@qq.com", "age": 21}]'
```
```python
>>> from rest_framework.renderers import JSONRenderer
>>> JSONRenderer().render(sers.data)
b'[{"name":"\xe5\xbc\xa0\xe4\xb8\x89","pwd":"123456zs","email":"zs@qq.com","age":19},{"name":"\xe6\x9d\x8e\xe5\x9b\x9b","pwd":"123456ls","email":"ls@qq.com","age":19},{"name":"\xe7\x8e\x8b\xe4\xba\x94","pwd":"123456ww","email":"ww@qq.com","age":21}]'
```
即：
```python
from rest_framework.renderers import JSONRenderer
# 查询用户对象
obj = UserInfo.objects.get(id=1)
# 创建序列化对象
ser = UserInfoSerializer(obj)
# 将得到的字段，转换为json数据
JSONRenderer().render(ser.data)
```

##  3. 关联对象嵌套序列化
### 3.1. 数据准备
#### 1. app1/models.py中定义模型类
[前一部分代码](#^8b1c56)
```python
# 模型Addr关联userinfo
class Addr(models.Model):
    # 收货地址模型
    user = models.ForeignKey('UserInfo', verbose_name=' 所属用户', on_delete=models.CASCADE)
    mobile = models.CharField(verbose_name='手机号', max_length=18)
    city = models.CharField(verbose_name='城市', max_length=10)
    info = models.CharField(verbose_name=' 详细地址', max_length=200)

    def __str__(self):
        return self.info
```
#### 2. app1/serializers.py中定义序列化器
[前一部分代码](#^9cc7bf)
```python
class AddrSerializer(serializers.Serializer):
    # 收货地址 序列化器
    mobile = serializers.CharField( max_length=18)
    city = serializers.CharField(max_length=10)
    info = serializers.CharField(max_length=200)
    # 返回关联模型对象的主键
    user = serializers.PrimaryKeyRelatedField()
```
#### 3. 重新生成迁移文件，并执行
```json
python manage.py makemigrations
python manage.py migrate
```
![](/assets/images/2307/Pasted%20image%2020230731113711.png)
#### 4. 添加练习数据
```python
>>> obj = UserInfo.objects.get(id=1)
>>> Addr.objects.create(user=obj, mobile='12345678910', city='北京', info='北京市海淀区')
<Addr: 北京市海淀区>
>>> Addr.objects.create(user=obj, mobile='223456789101', city='上海', info='上海市浦东区')
<Addr: 上海市浦东区>
```
#### 5. 序列化字段
```json
# 查询地址对象
addr1 = Addr.objects.get(id=1)
addr1
# 创建序列化对象
aser = AddrSerializer(addr1)
aser
aser.data
```

### 3.2. 关联字段序列化的方式
#### 1. PrimaryKeyRelatedField（较少使用）
- 返回关联字段的id
```python
user = serializers.PrimaryKeyRelatedField()
```
#### 2. StringRelatedField
- 返回关联字段的id
```python
user = serializers.StringRelatedField()
```
#### 3. 使用关联对象的序列化器
- 返回关联对象序列化器返回的所有字段
```python
user = UserInfoSerializer()
```
#### 4. SlugRelatedField
- 指定返回关联对象某个具体字段
```python
user = serializers.SlugRelatedField(read_only=True, slug_field='name')
```

# 二、反序列化操作（输入）
- 反序列化：将`json格式数据`，转换为`python对象`
- 进行反序列化操作时，会先对数据对象进行验证，验证通过的情况下再进行保存
- 反序列化时，初始化序列化器对象，将要被反序列化的数据传入`data参数`
- 进行修改、新增时使用反序列化
## 1. 数据验证
### 1.1. 校验数据
```json
>>> from app1.serializers import UserInfoSerializer
>>> user1 = {"name":"lisi", "pwd":"1234", "email":"123@123.com","age":18}
>>> ser = UserInfoSerializer(data=user1)
>>> ser.is_valid()
True
>>> ser.errors
{}
```





