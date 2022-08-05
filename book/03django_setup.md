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
