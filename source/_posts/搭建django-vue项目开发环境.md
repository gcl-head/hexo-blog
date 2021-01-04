---
title: 搭建django&vue项目开发环境
date: 2019-09-18 18:31:48
tags:
    -django
    -vue
    -跨域访问
categories: 
    -个人博客搭建
---
> 本篇将手把手教你从头构建以vue作为前端，以django作为后端的前后端分离的项目开发环境。
>
> 前端页面我选择使用VueJs进行渲染，django仅作为后端向前端提供api接口和管理数据库。

## 1、创建django项目

有两种方法创建django项目

1. 使用pycharm

   File>>New Project>>Django

2. 使用命令行（当pycharm是社区版或者使用其他ide时）

   ```bash
   django-admin startproject blog
   ```

创建完成后的文件目录结构：

```
.
├── manage.py
└── blog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

## 2、创建项目后端

进入项目根目录（当前目录）执行命令：

```bash
python3 manage.py startapp backend
```

创建完成后的文件目录结构：

```text
.
├── backend
│   ├── __init__.py
│   ├── admin.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── blog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

## 3、创建项目前端

进入项目跟根目录（当前目录）执行命令：

```bash
vue-init webpack frontend
```

创建完成后的文件目录结构：

```text
.
├── backend
│   ├── __init__.py
│   ├── admin.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── frontend
│   ├── README.md
│   ├── build
│   │   └── ....
│   ├── config
│   │   ├── dev.env.js
│   │   ├── index.js
│   │   ├── prod.env.js
│   │   └── test.env.js
│   ├── index.html
│   ├── package.json
│   ├── src
│   │   ├── App.vue
│   │   ├── assets
│   │   │   └── logo.png
│   │   ├── components
│   │   │   └── Hello.vue
│   │   └── main.js
│   ├── static
│   └── test
│       └── ...
├── manage.py
└── blog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

## 4、安装依赖并打包项目

确认当前所在目录为项目根目录，执行命令：

```bash
cd frontend
npm install
npm run build
```

构建完成后frontend目录下会多出一个dist文件夹：

```text
dist
├── index.html
└── static
    ├── css
    ├── fonts
    ├── img
    └── js
```

该文件夹放置打包后的静态网页文件，供django调用

## 5、django设定默认访问dist中的index.html

进入urls.py(即项目根目录/blog/urls.py)，使用通用视图创建模板控制器，让用户访问'/'时直接返回'dist/index.html'

```python
from django.contrib import admin
from django.conf.urls import url
from django.views.generic import TemplateView

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^$', TemplateView.as_view(template_name="index.html"))
]
```

进入settings.py(即项目根目录/blog/settings.py)，找到TEMPLATES配置项，修改如下：

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        # 'DIRS': [os.path.join(BASE_DIR, 'templates')]
        'DIRS': ['frontend/dist']
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

这样路由配置中的模板控制器就知道index.html的位置了

## 6、 配置静态文件搜索路径

打开 settings.py (项目根目录/blog/settings.py)，找到 STATICFILES_DIRS 配置项，配置如下:

```python
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "frontend/dist/static"),
]
```

这样Django不仅可以将/ulb 映射到index.html，而且还可以顺利找到静态文件

此时访问 / 我们可以看到使用Django作为后端的VueJS helloworld

## 7、提高调试速度(可选)

因为使用了django作为后端，因此每次运行必须先用`npm run build`打包后再启动django。如果直接使用`npm start`的话，只是启动了前端，无法获得后端的api。而打包花费的时间是很长的，大大降低了编写调试代码的效率。因此引入django第三方包django-cors-headers解决跨域问题。

安装：`pip3 install django-cors-headers`

进入 settings.py (项目根目录/blog/settings.py)，找到MIDDLEWARE配置项，修改如下：

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware',  # 增加corsheaders中间件
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

需要注意中间件列表是有序的，不能改变顺序。

并在末尾添加：

```python
CORS_ORIGIN_ALLOW_ALL = True  # 打开跨域功能
```

---

> 至此，我们的django和vue的前后端分离框架基本搭建完成了。