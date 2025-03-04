---
layout: post
title: "DRF框架-课程管理"
date: 2023-07-24 15:12
categories: [django, learning]
tags: [django, DRF, b站视频]
typora-root-url: ..
---

>1.  [3小时搞定DRF框架 | Django REST framework前后端分离框架实践](https://www.bilibili.com/video/BV1Dm4y1c7QQ/?p=5&share_source=copy_web&vd_source=4f9f06979d5997e5bab1def44a880876)
>2.  [liaogx/drf-tutorial: 快速入门Django REST framework，学会开发一套自己的RESTful API服务，并且自动生成API文档。视频学习地址： (github.com)](https://github.com/liaogx/drf-tutorial)

# 第二章 Django REST framework介绍和项目准备
## 1. 前后端分离
从以下四个方面理解：
- 1. 交互形式
	- 前端（客户端）负责页面展示，通过不同的HTTP方法与后端交互、获取数据、组装渲染。
	- 后端（服务器）为前端提供业务逻辑及数据准备，需按照约定的格式向前端提供可调用的API接口。
	- 前端后端约定好的沟通方式、接口规范，即RESTful API接口规范。
	- 前端后端传输数据的格式可为JSON、XML。
- 2. 代码组织方式
	- 前后端未分离
	- 前后端半分离
	- 前后端分离
- 3. 开发模式
	- 不分离的开发流程：
		- 提出需求 → 前端页面开发 → 翻译成模板 → 前后端对接 → 集成遇到问题 → 前端返工 → 后端返工 → 二次集成 → 集成成功 → 交付上线
	- 分离的开发流程：
		- 提出需求 → 约定接口规范数据格式 → 前后端并行开发 → 前后端对接 → 前端调试效果 → 集成成功 → 交付上线
- 4. 数据接口规范流程
	- ![](/assets/images/2307/20230725162549.png)

## 2. RESTful API接口规范
### 2.1. Restful API最佳实践
- 协议
- 域名
- 版本
- 路径
- HTTP方法
- 过滤信息（Filtering）
- 状态码（Status Codes）
- 错误处理（Error handling）
- 返回结果
- Hypermedia API
### 2.2. HTTP请求方法
- GET（SELECT）：从服务器去除资源（一项或多项）
- POST（CREATE）：在服务器新建一个资源
- PUT（UPDATE）：在服务器更新资源（客户端提供完整资源）
- PATCH（UPDATE）：在服务器更新资源（客户端提供部分资源）
- DELETE（DELETE）：从服务器删除资源
- HEAD：获取资源的元数据
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的

## 3. Pycharm搭建项目开发环境
### 3.1. settings.py中所作修改
```python
ALLOWED_HOSTS = ["*"]

TEMPLATES = [
    {
		...
        'DIRS': []
        ...
    },
]

LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
USE_TZ = False


STATIC_ROOT = BASE_DIR / "static"
STATICFIRES_DIRS = [
    BASE_DIR / "staticfiles"
]
```
### 3.2. 数据迁移，创建管理用户
```shell
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
# 设置的[用户名:密码]：[admin:admin123]
```

^1fafe7

### 3.3. 运行，登录管理界面
```html
# 点击运行后，浏览器进入页面，登录管理界面
http://127.0.0.1:8000/admin/
```

## 4. DRF安装
### 4.1. 添加requirements.txt文件
```python
asgiref==3.2.7
certifi==2020.4.5.1
chardet==3.0.4
coreapi==2.3.3
coreschema==0.0.4
Django==3.0.6
django-filter==2.2.0
djangorestframework==3.11.0
idna==2.9
importlib-metadata==1.6.0
itypes==1.2.0
Jinja2==2.11.2
Markdown==3.2.2
MarkupSafe==1.1.1
Pygments==2.6.1
pytz==2020.1
requests==2.23.0
sqlparse==0.3.1
uritemplate==3.0.1
urllib3==1.25.9
zipp==3.1.0
```
### 4.2. settings.py中所作修改
```python
INSTALLED_APPS = [
	...
    'rest_framework',    # RESTful API
    'rest_framework.authtoken',     # DRF自带的Token认证
    ...
]


# DRF的全局配置
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS' : 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE' : 50,    # 每页50条数据
    'DATETIME_FORMATE' : '%Y-%m-%d %H:%M:%S',    # 接口中返回的时间字段的显示格式
    'DEFAULT_RENDER_CLASSES' : [
        'rest_framework.renders.JSONRenderer',
        'rest_framework.renders.BrowsableAPIRenderer',
    ],
    'DEFAULT_PARSER_CLASSES' : [
        # 解析requset.data
        'rest_framework.parsers.JSONParser',
        'rest_framework.parsers.FormParser',
        'rest_framework.parsers.MultiPartParser',
    ],
    'DEFAULT_PERMISSION_CLASSES' : [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_AUTHENTICATION_CLASSES' : [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TockenAuthentication',
    ]
}
```
### 4.3. 修改url
```python
...
from django.urls import ... include

urlpatterns = [
    path('api-auth/', include('rest_framework.urls')),    # DRF的登录、退出
    ...
]
```
### 4.4. 运行登录
```shell
python manage.py makemigrations
python manage.py migrate
```
- 数据迁移之后，生成了authtoken表
![](/assets/images/2307/Pasted%20image%2020230728103803.png)
- 点击启动按钮，进入【http://127.0.0.1:8000/api-auth/login】
- 输入用户名密码【[admin:admin123](#^1fafe7)】
![](/assets/images/2307/Pasted%20image%2020230728104223.png) ^66263d
- 进入【accounts/profile/】页面，现还未开发此路由，所以显示如下，已登录成功：
![](assets/images/2307/Pasted%20image%2020230728104239.png)


# 第三章 DRF中的序列化Serializers
## 1. 新建course课程应用
### 1.1. 修改应用信息为中文
```python
from django.apps import AppConfig

class CourseConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'course'
    verbose_name = '课程信息'   # 新增代码
```
![](/assets/images/2307/Pasted%20image%2020230728112519.png)
### 1.2. 创建模型类
```python

```

---

# ↓遇到的问题：
1. [生成迁移文件](#^1fafe7)时，遇到了如下问题：
```shell
TypeError: argument of type 'PosixPath' is not iterable
```
- 解决方法：
	- django3.1之后的版本更换了语法，但本实操使用的django3.0.6也出现了此问题.
	- 故在settings.py文件中，将databases中default数据库的name路径转化为字符串：
> [在 Django 设置文件中使用 Pathlib - Adam Johnson](https://adamj.eu/tech/2020/03/16/use-pathlib-in-your-django-project/)
```python
# 原代码：
DATABASES = {
    'default': {
        ...
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# 修改为如下代码，将路径转化为字符串：
DATABASES = {
    'default': {
        ...
        'NAME': str(BASE_DIR / 'db.sqlite3'),
    }
}
```

2. [输入用户名密码点击登录](#^66263d)后，遇到了如下问题：
```shell
binascii.Error: Invalid base64-encoded string: number of data characters (213) cannot be 1 more than a multiple of 4
```
- 解决方法：**清除浏览器缓存**后，重新登录即可。
> [[Solved] binascii.Error: Invaild base64-encoded string: | 9to5Answer](https://9to5answer.com/binascii-error-invaild-base64-encoded-string-number-of-data-characters-1957-cannot-be-1-more-than-a-multiple-of-4)

3. python console无法打开，报错：
```shell
TypeError: an integer is required (got type bytes)
```
- 解决方法：
> [pycharm，使用python console报TypeError: an integer is required (got type bytes)的解决方法（python3.8）_Wwwwhy_　的博客-CSDN博客](https://blog.csdn.net/WangHY_XCJ/article/details/124278650)






