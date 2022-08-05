## 01 环境准备
1. python3
- 安装python虚拟环境(非必须): https://blog.csdn.net/weixin_45912307/article/details/108347547
- pip3 install  virtualenv -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
- pip3 install  virtualenvwrapper-win -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
- mkvirtualenv -p python 环境名称
- workon 查看虚拟环境列表
- workon 虚拟环境 切换虚拟环境
- deactivate 退出当前虚拟环境

2. pycharm代码编辑工具
3. git/gitlab代码托管工具
4. 注册git/gitlab账号，创建项目
5. 安装mysql
## 02 git使用
**1.** 生成秘钥
- ssh-keygen -t rsa -C "邮箱地址"
- 将生成的公钥放到账号秘钥管理中
**2.**  克隆代码到本地
- git clone 代码仓库地址
**3.** 查看状态
- git status
**4.** 提交代码到缓存区
- git add 文件名/目录名
**5.** 提交代码到本地仓库
- git commit -m "注释"
**6.** 提交代码到远程仓库
- git push origin master

**7.** 其他命令
链接：http://t.csdn.cn/3xPuQ
## 03 django_setup
1. 安装django
```
pip install -i http://pypi.douban.com
/simple/ --trusted-host pypi.douban.com Django==2.2.6
```
2. 创建项目
```
django-admin startproject api_test_platform
```

3. 创建应用
```
cd api_test_platform
python manage.py startapp 应用名称
```
4. 启动项目
- 方式1：python manage.py runserver
    - 默认ip端口：127.0.0.1:8000
- 方式2：指定ip端口
    - python manage.py runserver ip:端口
5. 注册子应用：
    - 将子应用的配置信息文件apps.py中的Config类添加到settings.py模块的INSTALLED_APPS列表中
    - 如：personal_center.apps.PersonalCenterConfig
6. setttins.py文件语言和时区设置
```
# 语言设置
LANGUAGE_CODE = 'zh-Hans'
# 时区设置
TIME_ZONE = 'Asia/Shanghai'
```
7. 定义视图
- 在views.py中编写请求处理逻辑
```
from django.shortcuts import render
def info(request):
    # 准备上下文：定义在字典中(测试数据)
    context = {'title': '接口测试平台个人中心'}
    return render(request, 'personal_center/info.html', context)
```
8. 配置url路由
- 在项目中定义URLconf
```
from django.conf.urls import include, url
url(r'^', include('personal_center.urls'))
```
- 在应用中定义URLconf
```
from django.conf.urls import url
from personal_center.views import info

urlpatterns = [
    url(r'^info/$', info)
]
```
9. 前端模板渲染
- 1）工程目录下新建templates目录，目录下新建personal_center目录
- 2）personal_center目录下新建info.html文件
## 03 工程创建和配置
### 3.1 创建项目
### 3.2 配置mysql数据库
1. 安装mysql-server
2. 新建项目数据库和管理用户
- 新建数据库 ：
```
create database api_test_platform charset=utf8
```
- 新建用户：
```
create user jsonLiu identified by '123456';
```
- 授权jsonLiu用户访问api_test_platform数据库：
```
grant all on api_test_platform.* to 'jsonLiu'@'%';
```
- 授权结束后刷新特权
```
flush privileges;
```
3. 安装PyMySQL第三方模块
```
pip install PyMySQL
```
3. 在settings.py文件中配置mysql
```
DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.mysql', # 数据库引擎
        # 'HOST': '169.254.148.206', # 数据库主机
        # 'PORT': 3306, # 数据库端口
        # 'USER': 'jsonLiu', # 数据库用户名
        # 'PASSWORD': '123456', # 数据库用户密码
        # 'NAME': 'api_test_platform' # 数据库名字
    }
}
```
4. 工程同名子目录的__init__.py文件中，添加如下代码
```
from pymysql import install_as_MySQLdb
install_as_MySQLdb()
```
### 3.3 配置redis数据库
1. 安装django-redis
```
pip install django-redis
```
2. 在dev.py文件中添加redis配置
```
# 配置redis数据库
CACHES = {
    "default": {
        # 默认的Redis配置项，采用0号Redis库
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://169.254.148.206:6379/0",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    },
    "session": { # session
        # 状态保持的Redis配置项，采用1号Redis库
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://169.254.148.206:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    },
}
SESSION_ENGINE = "django.contrib.sessions.backends.cache" # 修改session存储机制使用Redis保存
SESSION_CACHE_ALIAS = "session" # 使用名为"session"的Redis配置项存储session数据
```
### 3.4 配置工程日志
#### 3.4.1 配置工程日志
在dev.py文件的尾部放置日志配置
```
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,  # 是否禁用已经存在的日志器
    'formatters': {  # 日志信息显示的格式
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(lineno)d %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(module)s %(lineno)d %(message)s'
        },
    },
    'filters': {  # 对日志进行过滤
        'require_debug_true': {  # django在debug模式下才输出日志
            '()': 'django.utils.log.RequireDebugTrue',
        },
    },
    'handlers': {  # 日志处理方法
        'console': {  # 向终端中输出日志
            'level': 'INFO',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
            'formatter': 'simple'
        },
        'file': {  # 向文件中输出日志
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': os.path.join(os.path.dirname(BASE_DIR), 'logs/meiduo.log'),  # 日志文件的位置
            'maxBytes': 300 * 1024 * 1024,
            'backupCount': 10,
            'formatter': 'verbose'
        },
    },
    'loggers': {  # 日志器
        'django': {  # 定义了一个名为django的日志器
            'handlers': ['console', 'file'],  # 可以同时向终端与文件中输出日志
            'propagate': True,  # 是否继续传递日志信息
            'level': 'INFO',  # 日志器接收的最低日志级别
        },
    }
}
```
#### 3.4.2 准备日志文件目录
项目目录下新建logs文件
#### 3.4.3 日志器的使用
#### 3.4.4 git管理工程日志

### 3.5 配置前端静态文件
## 04 模块功能实现
## 05 项目部署
### 5.1 用户注册

### 5.2 用户登陆
### 5.3 个人中心 