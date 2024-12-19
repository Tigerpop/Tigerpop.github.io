---
layout: posts
title: flask-study
date: 2024-8-9 10:21:2
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "网络学习"
tags:
- "网络"
- "flask"
- "后端"

---



# Flask整体框架流程

![截屏2024-01-04 15.28.48](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-04%2015.28.48.jpg)

## 简单例子

```python
# 脚本 要以 app.py 命名。然后用 flask run。
from flask import Flask 

app = Flask(__name__)

# 路由装饰器 装饰一个 视图函数。
@app.route("/")
def hello_world():
    return "<p>Hello,world!</p>"

@app.route('/index')
def index_test():
    return 'Index 页面'


if __name__=='__main__':
    # 启动服务器
    app.run()
```



建立一个项目，项目下由app.py、templates/index.html、static/index.css 组成。

``` python
# app.py
# 脚本 要以 app.py 命名。然后用 flask run。
from flask import Flask, jsonify, render_template

app = Flask(__name__)

# 路由装饰器 装饰一个 视图函数。
@app.route("/")
def home():
    return "<b>Flask Home</b>"

@app.route('/index')
def index_test():
    # return 'Index 页面2'
    
    # 模版渲染，所谓渲染就是 从后端找变量给前端填空。
    # 自动去templates下找 index.html。
    return render_template('index.html',name='法外狂徒')

    # json
    # return jsonify({'name':'张三','age':100})

if __name__=='__main__':
    # 启动服务器
    # debug 开启调试模式，修改代码后，自动重启。
    # 端口可以自己指定，
    # 0.0.0.0 表示本机所有IP都能访问，包括本机局域网IP（可以让局域网都能访问）。
    app.run(debug=True,port=5000,host='0.0.0.0') 
```

```html
 <!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <!-- <link rel="stylesheet" href="../static/index.css"> -->
    <link rel="stylesheet" href="{{ url_for('static',filename='index.css')}}">
</head>
<body>
    <h2>Index</h2>
    <hr>
    <h4>name: {{ name }}</h4>
</body>
</html>
```

```css

h4 {
    color: red;
}
```

![截屏2024-01-05 10.13.52](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-05%2010.13.52.jpg)

​		注意上面的 `app = Flask(__name__)` 是在 “第一个flask项目” 下创建的，app对象实际上的执行 是发生在“第一个flask项目”下的。



# 项目的拆分

![截屏2024-01-05 11.13.35](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-05%2011.13.35.jpg)

直接从App包 下调用 __init__.py 中的方法创建app对象就 好了。一个 App 就是一个 flask 应用。

app.py

```python
# 直接从App包 下调用 __init__.py 中的方法创建app对象就 好了。

from App import create_app 

app = create_app()

if __name__ == '__main__':
    app.run(debug=True)
```

App/ __init__ .py

```python
# 导入上一层级的 App 的包的时候，这个 __init__.py自动执行
# 一个 App 就是一个 flask 应用。
# 注意不要循环导入，这里统一外部向 __init__ 导入；

from flask import Flask 
from .views import blue 


# 不必每一次 导入app包都新建一个 app 对象，所以封装进方法等待调用。jdong
def create_app():
    app = Flask(__name__)
    
    # 给app对象 注册 blue蓝图，把他们关联上。
    app.register_blueprint(blueprint=blue)
    
    return app 
```

App/views.py

```python
from flask import Blueprint 

# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块
blue2 = Blueprint('product',__name__) 

# 给蓝图对象加上 路由；
@blue.route('/')
def index():
    return 'home'

@blue2.route('/goods')
def index():
    return 'goods'
```



# 路由

![截屏2024-01-05 20.41.25](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-05%2020.41.25.jpg)

```python
# views.py 下补充 

# 路由参数
# string 
@blue.route('/string/<string:name>/')
def get_string(name):
    print(type(name)) 
    return name
# int
@blue.route('/int/<int:age>/')
def get_int(age):
    print(type(age)) 
    return str(age) 
# uuid
@blue.route('/getuuid')
def get_uuid2():
    import uuid 
    return str(uuid.uuid4())
@blue.route('/uuid/<uuid:id>/')
def get_uuid(id):
    print(type(id)) 
    return str(id) 
# any
@blue.route('/any/<any(apple,orange,banana):id>/')
def get_any(id):
    print(type(id)) 
    return str(id) 

# method : 请求方式 默认 get请求
# 如果要 get 和 post 都支持，需要改写一下。
# http状态码 1XX 成功一部分；2XX成功；3XX重定向；4XX客户端错误；5XX服务端错误。
@blue.route('/methods',methods=['GET','POST'])
def get_methods():
    return 'methods'
```

```python
# spider.py 

import requests 

# get 请求
res = requests.get('http://127.0.0.1:5000/methods')
print(res.text)

# post 请求
res = requests.post('http://127.0.0.1:5000/methods')
print(res.text)
```



# Flask请求request/响应response

![截屏2024-01-06 17.39.53](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-06%2017.39.53.jpg)

```python
from flask import Blueprint, Response,jsonify, render_template,request


# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块
blue1 = Blueprint('product1',__name__) 
blue2 = Blueprint('product2',__name__) 

# 客户端向 服务端 发送请求
@blue.route('/request/',methods=['GET','POST'])
def get_request():
    print('look here ! this is:',request)
    
    # 重要属性
    print(request.method)# 'get' 'post' ...
    print(request.args)  # 获取 GET 请求的参数
    print(request.args.get('name'))
    print(request.form)  # 获取 POST 请求的参数
    print(request.form.get('name'))
    print(request.cookies)
    return 'request ok!'

# response 服务端 向 客户端发送响应
@blue.route('/response/')
def get_response():
    pass 
    # 响应的几种方式
    # 1.返回字符串 return 'response is ok!'
    # 2.模版渲染,需要渲染相应的html文件.     
    # return render_template('index.html',name='zs',age=100)
    # 3.返回json数据。
    # data = {'name':'李四','age':100}
    # # return data 
    # return jsonify(data) # 序列化一下，把字典变成str。
    # 4.自定义response对象。
    html = render_template('index.html',name='小城',age=100)
    print(html,type(html))    
    res = Response(html)
    return res 
```

```python
import requests 
# 模拟前端的请求。

# get 请求 ?后加参数。
res = requests.get('http://127.0.0.1:5000/request/?name=lisi&name=zhangsan&age=22')
print(res.text)

# post 请求 字典形式加参数。
res = requests.post('http://127.0.0.1:5000/request/',
                    data={'name':'lucy','age':33},
                    cookies={'name':'hello'})
print(res.text)

# 上面代码 模拟发送了两次请求，每一个请求都会触发一次 view.py中的request方法。
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <!-- <link rel="stylesheet" href="../static/index.css"> -->
    <link rel="stylesheet" href="{{ url_for('static',filename='index.css')}}">
</head>
<body>
    <h2>Index</h2>
    <hr>
    <p>name: {{ name }}</p>
    <p>age: {{age}}</p>
</body>
</html>
```



# 重定向

```python
from flask import Blueprint, Response,jsonify, redirect, render_template,request, url_for

# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块
blue1 = Blueprint('product1',__name__) 

# 客户端向 服务端 发送请求
@blue.route('/request/',methods=['GET','POST'])
def get_request():
    print('look here ! this is:',request)
    
    # 重要属性
    print(request.method)# 'get' 'post' ...
    print(request.args)  # 获取 GET 请求的参数
    print(request.args.get('name'))
    print(request.form)  # 获取 POST 请求的参数
    print(request.form.get('name'))
    print(request.cookies)
    return 'request ok!'

# response 服务端 向 客户端发送响应
@blue.route('/response/')
def get_response():
    pass 
    # 响应的几种方式
    # 1.返回字符串 return 'response is ok!'
    # 2.模版渲染,需要渲染相应的html文件.     
    # return render_template('index.html',name='zs',age=100)
    # 3.返回json数据。
    # data = {'name':'李四','age':100}
    # # return data 
    # return jsonify(data) # 序列化一下，把字典变成str。
    # 4.自定义response对象。
    html = render_template('index.html',name='小城',age=100)
    print(html,type(html))    
    res = Response(html)
    return res 

# Redirect 重定向
@blue.route('/redirect')
def make_redirect():
    pass
    # 1.
    # return redirect('https://www.qq.com') # 重定向到外部网站。
    # 2.
    # return redirect('/response/') # 重定向到本地path路径，可能会触发路由修饰器的 视图函数。
    # 3.
    # 反向解析 url_for 通过视图函数 反过来找到 路由。
    # url_for('蓝图名称.视图函数名') 注意是蓝图名称，不是蓝图对象。
    ret = url_for('user.get_response',) # ret 其实是路由 path。
    print('ret is:',ret) # 打印 ret is: /response/
    # url_for的重定向是能够传参的。
    ret2 = url_for('user.get_request',name='张飞',age=100)
    return redirect(ret2)
```



# cookie实现

​		cookie的记忆是基于用户而不是浏览器；![截屏2024-01-06 20.23.45](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-06%2020.23.45.jpg)

![截屏2024-01-07 20.07.43](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-07%2020.07.43.jpg)

```python
# views.py
from flask import Blueprint, redirect, render_template, request


# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块

# 首页
@blue.route('/')
@blue.route('/home/')
def home():
    # 4. 获取 cookie
    username = request.cookies.get('user')
    
    return render_template('/home.html',username=username)

# 登录
@blue.route('/login/',methods=['POST','GET'])
def login():
    # GET 访问登录页面
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        pass 
        # 1.获取前端提交过来的数据
        username = request.form.get('username')
        password = request.form.get('password')
        # 2.模拟登录，验证用户名密码
        if username=='chen' and password=='123':
            # success  则重定向到 home 中
            response = redirect('/home')
            print(response,'look here')
            # 3. 设置 cookie ，cookie中不能有中文。
            response.set_cookie('user',username,max_age=3600*24*7)
            return response
        else:
            return '用户名或密码错误'

# 注销
@blue.route('/logout')
def logout():
    response = redirect('/home')
    # 5.删除cookie
    response.delete_cookie('user')
    return response
```

```html
<!-- /home.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>

    <!-- <link rel="stylesheet" href="../static/index.css"> -->
    <link rel="stylesheet" href="{{ url_for('static',filename='index.css')}}">
</head>
<body>
    <h2>首页</h2>
    <hr>

    {% if username %}
        <p>
            当前用户是:{{username}}
            <a href="/logout">注销</a>
        </p>
    {% else %}
        <a href="/login">登录</a>
    {% endif %}

    <!-- 注意要避免前端跳前端，那样会数据渲染不了,一定要经过后端。 -->
    <!-- /login 的路径在后端有对应的处理，那么就说明点击链接时涉及到后端路由。
        否则，可能是前端路由或者直接的页面跳转。 -->
    <!-- <a href="/login">登录</a> -->

</body>
</html>
```

```html
<!-- /login.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录</title>

    <!-- <link rel="stylesheet" href="../static/index.css"> -->
    <link rel="stylesheet" href="{{ url_for('static',filename='index.css')}}">
</head>
<body>
    <h2>登录</h2>
    <hr>

    <!-- 登录表格 -->
    <!-- <form action="" method="post">  写法1 -->
    <form action="{{url_for('user.login')}}" method="post"> <!-- 写法2 用反向解析 -->
        <p>
            用户名:<input type="text" name="username">
        </p>
        <p>
            密码:<input type="password" name="password">
        </p>
        <p>
            <button>提交</button>
        </p>
    </form>

</body>
</html>
```



# session实现

![截屏2024-01-07 20.46.56](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-07%2020.46.56.jpg)

![截屏2024-01-07 21.18.45](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-07%2021.18.45.jpg)

```python
# views.py
from flask import Blueprint, redirect, render_template, request, session


# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块

# 首页
@blue.route('/')
@blue.route('/home/')
def home():
    #  获取 session
    username = session.get('user')
    return render_template('/home.html',username=username)

# 登录
@blue.route('/login/',methods=['POST','GET'])
def login():
    # GET 访问登录页面
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        pass 
        # 1.获取前端提交过来的数据
        username = request.form.get('username')
        password = request.form.get('password')
        # 2.模拟登录，验证用户名密码
        if username=='chen' and password=='123':
            response = redirect('/home/')
            # 设置 session 
            session['user'] = username 
            session.permanent = True 
            return response
        else:
            return '用户名或密码错误'

# 注销
@blue.route('/logout')
def logout():
    response = redirect('/home')
    # 删除 session
    session.pop('user')  
    return response
```

```python
# __init__.py
# 导入上一层级的 App 的包的时候，这个 __init__.py自动执行
# 一个 App 就是一个 flask 应用。
# 注意不要循环导入，这里统一外部向 __init__ 导入；

import datetime
from flask import Flask 
from .views import blue


# 不必每一次 导入app包都新建一个 app 对象，所以封装进方法等待调用。jdong
def create_app():
    app = Flask(__name__)
    
    # 给app对象 注册 blue蓝图，把他们关联上。
    app.register_blueprint(blueprint=blue)
    
    # session 配置
    print(app.config)
    app.config['SECRET_KEY'] = '123' # 对称加密
    app.config['PERMANENT_SESSION_LIFETIME'] = datetime.timedelta(days=8)
    
    return app 
```



# template

Flask 使用 jinja2模版引擎。

![截屏2024-01-11 10.42.45](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-11%2010.42.45.jpg)

![截屏2024-01-11 10.45.46](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-11%2010.45.46.jpg)

![截屏2024-01-11 10.46.38](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-11%2010.46.38.jpg)

![截屏2024-01-11 19.43.24](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-11%2019.43.24.jpg)

view.py

```python
from flask import Blueprint, render_template 

# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块

@blue.route('/')
def home():
    pass 
    data = {
        'name':'ikun ikun ikun',
        'age':7,
        'likes':['ball','sing','dance','code']
    }
    # return render_template('home.html',**data)
    # return render_template('base.html')
    # return render_template('child1.html')
    return render_template('child2.html',**data)
```

home.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    <h2>Home</h2>
    <hr>
    {# 模版语言的注释 #}
    <h4>变量:</h4>
    <p>name:{{ name }}</p>
    <p>age:{{age}}</p>
    <p>likes:{{likes}}</p>

    <hr>
    <h4>标签:</h4>
    <h5>if语句</h5>
    {% if age >= 18 %} 
        <p>{{name}}已经成年了</p>
    {% elif age>=6 %}
        <p>可以上学了</p>
    {% else %}
        <p>{{name}}还是小孩</p>
    {% endif %}  

    <h5>for循环</h5>
    {% for like in likes %}
        {% if loop.first %}
            <p style="color:red;">{{like}}</p>
        {% elif loop.last %}
            <p style="color:blue;">{{like}}</p>
        {% else %}
            <p>{{like}}</p>
        {% endif %}  
        index:{{ loop.index }}
        index2:{{ loop.index0 }}
        revindex:{{ loop.revindex }}
        revindex0:{{ loop.revindex0 }}
    {% endfor %}
    
</body>
</html>  
```

base.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>父模版</title>

    {# 在html的head中导入css文件，static中 #}
    <link rel="stylesheet" href="{{url_for('static',filename='css/base.css')}}">        
    {% block extcss %}
    {% endblock %}
</head>
<body>
    {% block head %}
    {% endblock %}
    <hr>
    {% block content %}
    {% endblock %}
    <hr>
    {% block foot %}
    {% endblock %}

    {# 导入js文件，static中 #}
    <script src="{{url_for('static',filename='js/base.js')}}"></script>
    {% block extjs %}
    {% endblock %}
</body>
</html>  
```

child1.html

```html
{% extends 'base.html' %}

{% block head %}
    <div>
        <p>ok,ok,ok.</p>
    </div>
{% endblock %}
```

child2.html

```html
{% extends 'child1.html' %} {# 继承 #}

{% block head %}
    {{ super() }} {# 保留父辈block head内容 #}
    <p>python</p> {# 如果没有super就,覆盖重写了 #}
{% endblock %}

{% block content %}
    {% include 'child2_include.html' %} {# 导入include内容 #}
    <p>Flask content </p> 
{% endblock %}

<!-- 宏定义 ： python的函数 -->
{% macro person(name,age) %}    
    <b>姓名 : {{name}}, 年龄 : {{age}} </b>
{% endmacro %}

{% block foot %}
    {{ person('小鸟',100) }}
    
    {# 过滤器 #}
    <p>{{name | capitalize }}</p>
    <p>{{name | title }}</p>
    <p>{{name | upper }}</p>
    <p>{{name | upper | first |lower }}</p>

{% endblock %}
```

child2_include.html

```html
<div>我是child2中include的内容</div>
```



# 模型

## ORM

object relational mapping 对象关系映射；

数据库的表 映射到对象，对对象操作。 

`pip install flask-sqlalchemy  pip install flask-migrate   pip install pymysql`



![截屏2024-01-14 19.49.26](flask_study.assets/%E6%88%AA%E5%B1%8F2024-01-14%2019.49.26.jpg)

```python
#  __init__.py

# 导入上一层级的 App 的包的时候，这个 __init__.py自动执行
# 一个 App 就是一个 flask 应用。
# 注意不要循环导入，这里统一外部向 __init__ 导入；

from flask import Flask 
from .views import blue
from .exts import init_exit


# 不必每一次 导入app包都新建一个 app 对象，所以封装进方法等待调用。jdong
def create_app():
    app = Flask(__name__)
    
    # 给app对象 注册 blue蓝图，把他们关联上。
    app.register_blueprint(blueprint=blue)
    
    # 配置数据库
    db_uri = 'sqlite:///sqlite3.db'
    app.config['SQLALCHEMY_DATABASE_URI'] = db_uri # 配置数据库路径
    
    # 把 插件和app关联上
    init_exit(app=app)
    
    return app 
```

```python
# exts.py

# exts.py 插件管理
# 扩展的第三方插件

# 1.导入第三方插件。
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

# 2.初始化。
db = SQLAlchemy()    # ORM (让类变成模型)
migrate = Migrate()  # 数据迁移 (让模型变成表)

# 3.和app对象绑定。
def init_exit(app):
    db.init_app(app=app)
    migrate.init_app(app=app,db=db)
```

```python
# models.py

from .exts import db 

# 模型中的 （对应）   数据库中的
# 类        -->     表
# 类属性     -->     表的字段
# 对象       -->     表中的一行数据

# 模型 (就是一个继承了db.Model的类)
class User(db.Model):
    # 表名
    __tablename__ = 'tb_user'
    # 定义表的字段 
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    name = db.Column(db.String(30),unique=True,index=True)
    age = db.Column(db.Integer,default=1)
    sex = db.Column(db.Boolean,default=True)
    salary = db.Column(db.Float,default=1000000000,nullable=False)
```

```python
# views.py

from flask import Blueprint 
from .models import * 
# 通过导入models模块，让models模块执行，间接让exits模块执行，也就有了db。

# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块

# 给蓝图对象加上 路由；
@blue.route('/')
def index():
    return 'home' 
# 路由参数
# string 
@blue.route('/string/<string:name>/')
def get_string(name):
    print(type(name)) 
    return name
```

进入 app.py 文件夹所在的目录，执行

`flask db init（创建迁移文件夹migrates;只调用一次）  `
`flask db migrate (生成迁移文件;自动搜索继承了db.Model的类) `
`flask db upgrade (把类都转换成表)  `
`flask db downgrade (撤回把类都转换成表)`

每一次执行 `flask db migrate` 都会在migrations/versions 中生成新的py文件，在表格变动的时候记得生成，然后 再 upgrade 变成表，在instance 下的db文件可见这些表。



注意：

Flask使用migrate迁移后，无法检测到models，无法生成表，则 ***\*只需要在 app.py 中导入 models.py 中的类即可。\****



## 模型表操作

![截屏2024-02-06 23.39.34](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-06%2023.39.34.jpg)

```python
# view.py

from operator import and_
from flask import Blueprint, render_template, request
from sqlalchemy import desc 
from .models import *

# 蓝图用于规划 url
blue = Blueprint('user',__name__) # __name__表示导入到当前模块
blue1 = Blueprint('product1',__name__) 
blue2 = Blueprint('product2',__name__) 

# 给蓝图对象加上 路由；
@blue.route('/useradd')
def user_add():
    u = User()
    u.name = 'kun'
    u.age = 24 
    db.session.add(u)   # u对象添加到session中。
    db.session.commit() # 同步到数据库中.
    return 'success!'

@blue.route('/useradds')
def user_adds():
    users = []
    for i in range(10,30):
        u = User()
        u.name = '斌斌' + str(i)
        u.age = i 
        users.append(u)
    try:
        db.session.add_all(users)
        db.session.commit() # 事物提交
    except Exception as e:
        db.session.rollback() # 回滚
        db.session.flush() # 清空缓存
        return 'fail !' + str(e)
    return 'success !!'

@blue.route('/userdel')
def user_del():
    u = User.query.first()  # 先查到，再删除。
    db.session.delete(u)
    db.session.commit()
    return 'success !'

@blue.route('/userupdate')
def user_update():
    u = User.query.first()  # 先查到，再删除。
    u.age = 1000
    db.session.commit()
    return 'success !'

@blue.route('/userget/')
def user_get():
    users = User.query.all()
    # print(users) # class 'list'
    # print(User.query) # 查询语句
    
    # filter()
    users = User.query.filter()
    # print(users,type(users)) # 查询集,可以不停的 filter再filter.
    
    # get()
    user = User.query.get(8)
    # print(user,type(user)) # user 对象.
    # print(user.name,user.age)
    
    # filter()
    users = User.query.filter(User.age==20)
    users = User.query.filter_by(age=20)
    users = User.query.filter(User.age>20)
    print(list(users))
    
    # limit() 前几条
    # offset() 跳过前几条
    users = User.query.offset(3).limit(4)
    print(list(users))
    
    # order by 
    users = User.query.order_by('age')
    users = User.query.order_by(desc('age'))
    print(list(users))
    
    # 逻辑运算
    users = User.query.filter(and_(User.age>20,User.age<25))
    print(list(users))
    users = User.query.filter(User.name.contains('3'))
    print(list(users))
    users = User.query.filter(User.name.in_([10,20,30,40]))
    print(list(users))
    
    return 'success !'

# paginate 对象，在网页中/paginate/？后加的参数 page=1 就是第一页的内容。
@blue.route('/paginate/')
def get_paginate():
    # 默认显示第一页
    page = int(request.args.get('page',1))
    # 每页显示数据量
    per_page = int(request.args.get('per_page',5))
    print(page,type(page))
    print(per_page,type(per_page))
    # 下面的只是默认形参，实际以网页输入的参数为准。
    p = User.query.paginate(page=page,per_page=per_page,error_out=False)
    print(p.items) # 返回当前页的内容列表
    print(p.has_next) # 是否有下一页
    print(p.has_prev)
    print(p.total)
    
    return render_template('paginate.html',p=p)
    
```

```python
# model.py

from .exts import db 

class User(db.Model):
    # 表名
    __tablename__ = 'user1'
    # 定义表的字段 
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    name = db.Column(db.String(30),unique=True)
    age = db.Column(db.Integer,default=1)

    def __repr__(self) -> str: # 内置字符串函数
        return self.name

```

先去bootstrap官网下载文件，把对应的bootstrap.css 文件放到app/static/css文件夹下。照着文档指导配合自己的逻辑关系写即可。

```html
<!-- paginate.html>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>分页演示</title>
    <!-- 先去bootstrp官网下载对应的css文件。 -->
    <link rel="stylesheet" href="{{ url_for('static',filename='css/bootstrap.css')}}">
</head>
<body>
    <div class="container">
        <h2>分页演示</h2>
        <hr>

        <!-- 编程的方式插入具体的内容 -->
        <ul class="list-group">  
            {% for user in p.items %}
                <li class="list-group-item">{{ user.name }}</li>    
            {% endfor %}
        </ul>
    </div>
    <hr>
    <!-- 加上页脚 也就是分页内容 -->
    <ul class="pagination">
        <!-- 上一页 -->
        <li class="page-item">
            {% if p.has_prev %}
                <a class="page-link" href="/paginate/?page={{ p.prev_num }}&per_page=3" aria-label="Previous">
            {% else %}
            <a class="page-link" href="javascript:;" aria-label="Previous">
            {% endif %}
            <span aria-hidden="true">&laquo;</span>
            </a> 
        </li>
        <!-- 中间页码 -->
        {% for i in range(p.pages) %}   
            {% if p.page == i+1 %} <!-- 标亮当前页面 -->
                <li class="page-item active">
            {% else %}
                <li class="page-item">
            {% endif %}
            <!-- <li class="page-item"> -->
                <a class="page-link" href="/paginate/?page={{ i+1 }}&per_page=3">{{ i + 1 }}</a>
            </li>
        {% endfor %}
        <!-- 下一页 -->
        <li class="page-item">
            {% if p.has_next %}
                <a class="page-link" href="/paginate/?page={{ p.next_num }}&per_page=3" aria-label="Next">
            {% else %}
            <a class="page-link" href="javascript:;" aria-label="Next">
            {% endif %}
            <span aria-hidden="true">&raquo;</span>
            </a> 
        </li>
    </ul> 
</body> 
</html>
```



# 图书馆测试项目

![截屏2024-02-28 08.45.02](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-28%2008.45.02.jpg)

app.py

```python
# 直接从App包 下调用 __init__.py 中的方法创建app对象就 好了。

from App import create_app 
from App.models import *

app = create_app()

if __name__ == '__main__':
    app.run(debug=True)
```

`__init__.py`

```python
# 导入上一层级的 App 的包的时候，这个 __init__.py自动执行
# 一个 App 就是一个 flask 应用。
# 注意不要循环导入，这里统一外部向 __init__ 导入；

from flask import Flask 
from .views import blue
from .exts import init_exts


# 不必每一次 导入app包都新建一个 app 对象，所以封装进方法等待调用。jdong
def create_app():
    app = Flask(__name__)
    
    # 给app对象 注册 blue蓝图，把他们关联上。
    app.register_blueprint(blueprint=blue)
    
    # 配置数据库
    # db_uri = 'sqlite:///sqlite3.db'
    db_uri = 'mysql+pymysql://root:cys123456@localhost:3306/bookdb' # mysql
    app.config['SQLALCHEMY_DATABASE_URI'] = db_uri # 配置数据库路径
    app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False 
    
    # 把 插件和app关联上
    init_exts(app=app)
    
    
    return app 
```

exes.py

```python
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

db = SQLAlchemy() 
migrate = Migrate()

def init_exts(app):
    db.init_app(app=app)
    migrate.init_app(app=app,db=db)
```

models.py 定义模型结构

```python
from .exts import db 

# 作者 
class Author(db.Model):
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    name = db.Column(db.String(20) )
    age = db.Column(db.Integer,default=1)
    sex = db.Column(db.Boolean,default=True) 
    email = db.Column(db.String(200))
    # 关系 (关联上book表) 1对多
    # 如果要知道 一个作者有多少作品，Author.books 关联的表book调用backref的值可以知道对应的作者。
    books = db.relationship('Book',backref='auther',lazy='dynamic')  
    
# 书籍
class Book(db.Model):
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    title = db.Column(db.String(100),unique=True)
    date = db.Column(db.DateTime)
    # 1对多，外键。
    author_id = db.Column(db.Integer,db.ForeignKey(Author.id)) 

# 中间表 (书籍-出版社) 如果关联中间表，则需要 secondary 参数
# 设置这两个字段为主键的目的是确保在中间表中的每一对关系都是唯一的
book_publisher = db.Table('book_publisher',
    db.Column('book_id',db.Integer,db.ForeignKey('book.id'),primary_key=True),
    db.Column('publisher_id',db.Integer,db.ForeignKey('publisher.id'),primary_key=True))

# 出版社
class Publisher(db.Model):
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    name = db.Column(db.String(20),unique=True)
    address = db.Column(db.String(200))
    province = db.Column(db.String(100))
    country = db.Column(db.String(100))
    website = db.Column(db.String(100))
    # 多对多，关联book表。
    # 出版社调用 books 可以知道出版社对应的书籍，某一本书调用backref的值知道对应出版社。
    books = db.relationship('Book',backref='publishers',
                            secondary=book_publisher,lazy='dynamic')    
```

模型构建好以后，就可以进入 app.py 文件夹所在的目录，执行

`flask db init（创建迁移文件夹migrates;只调用一次）  `
`flask db migrate (生成迁移文件;自动搜索继承了db.Model的类) `
`flask db upgrade (把类都转换成表)  `
`flask db downgrade (撤回把类都转换成表)`

构建完表之后，就可以往表内写各种内容了。如果是vscode插件操作mysql可能出现数据库同步出现问题，此时需要找到migrations文件夹下最下面也就是最新的一个.py文件的名称，此名称去掉下划线，放入mysql数据库的alembic_version中。

![截屏2024-02-28 08.59.23](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-28%2008.59.23.jpg)

![截屏2024-02-28 08.58.37](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-28%2008.58.37.jpg)

​		通过模型让mysql构建好表的结构之后，我们在表内写入数据。

![截屏2024-02-28 09.02.36](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-28%2009.02.36.jpg)

![截屏2024-02-28 09.02.56](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-28%2009.02.56.jpg)

![截屏2024-02-28 09.03.31](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-28%2009.03.31.jpg)

![截屏2024-02-28 09.03.48](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-28%2009.03.48.jpg)

view.py 视图脚本,基于视图脚本写各个html的前端逻辑

```python
from operator import and_
from flask import Blueprint, render_template, request
from sqlalchemy import desc 
from .models import *

# 蓝图用于规划 url
blue = Blueprint('book',__name__) # __name__表示导入到当前模块

@blue.route('/')
@blue.route('/bookindex/')
def book_index():
    return render_template('book_index.html')

@blue.route('/booklist/')
def book_list():
    books = Book.query.all()
    return render_template('book_list.html',books = books)

@blue.route('/bookdetail/<int:bid>/')
def book_detail(bid):
    book = Book.query.get(bid)
    return render_template('book_detail.html',book=book)

@blue.route('/authordetail/<int:aid>/')
def author_detail(aid):
    author = Author.query.get(aid)
    return render_template('author_detail.html',author=author)

@blue.route('/publisherdetail/<int:pid>/')
def publisher_detail(pid):
    publisher = Publisher.query.get(pid)
    return render_template('publisher_detail.html',publisher=publisher)
```

book_index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>书籍</title>
</head>
<body>
    <h2>书籍首页</h2>   
    <hr>

    <a href="/booklist/">查看所有书籍</a>
</body> 
</html>
```

book_list.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>书籍</title>
</head>
<body>
    <h2>书籍列表</h2>   
    <hr>
    <ul>
        {% for book in books %}
            <li>
                <!-- <a href="/bookdetail/{{ book.id }}">{{ book.title }}</a> -->
                <a href="{{ url_for('book.book_detail',bid=book.id) }}">{{ book.title }}</a>
            </li>
        {% endfor %}
    </ul>

</body> 
</html>
```

book_detail.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>书籍</title>
</head>
<body>
    <h2>书籍详情</h2>   
    <hr>

    <p>书籍标题：{{ book.title }}</p>
    <p>书籍出版时间：{{ book.date }}</p>
    <p>作者：
        <a href="/authordetail/{{ book.auther.id }}">{{ book.auther.name }}</a>
    </p>
    <p>出版社：
        {% for publisher in book.publishers %}
            <a href="/publisherdetail/{{ publisher.id }}">{{ publisher.name }}</a>
        {% endfor %}
    </p>

</body> 
</html>
```

author_detail.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>书籍</title>
</head>
<body>
    <h2>作者详情</h2>   
    <hr>
    <p>作者名称：{{ author.name }}</p>
    <p>作者年龄：{{ author.age }}</p>
    <p>作者性别：{{ author.sex }}</p>
    <p>作者Email：{{ author.email }}</p>
    <hr>
    <p>我的作品
        {% for book in author.books %}
            <a href="/bookdetail/{{ book.id }}">{{ book.title }}</a>
        {% endfor %}
    </p>

</body> 
</html>
```

publisher_detail.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>书籍</title>
</head>
<body>
    <h2>出版社详情</h2>   
    <hr>
    <p>出版社名称：{{ publisher.name }}</p>
    <p>出版社名称：{{ publisher.address }}</p>
    <p>出版社名称：{{ publisher.city }}</p>
    <p>出版社名称：{{ publisher.province }}</p>
    <p>出版社名称：{{ publisher.website }}</p>
    <hr>

    <p>出版社的出版书籍
        {% for book in publisher.books %}
            <a href="/bookdetail/{{ book.id }}">{{ book.title }}</a>
        {% endfor %}
    </p>


</body> 
</html>
```



# 插件使用

## 缓存例子

![截屏2024-02-29 09.35.45](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-29%2009.35.45.jpg)

![截屏2024-02-29 09.42.04](flask_study.assets/%E6%88%AA%E5%B1%8F2024-02-29%2009.42.04.jpg)

​		使用flask插件 就三个步骤，先下载，再ext.py中初始化，最后再视图中使用。

​		

# 中间件（钩子）

## 切面编程

![截屏2024-03-01 10.56.21](flask_study.assets/%E6%88%AA%E5%B1%8F2024-03-01%2010.56.21.jpg)

views.py

```python
from operator import and_
import time
from flask import Blueprint, render_template, request
from sqlalchemy import desc 
from .models import *
from .exts import cache 

# 蓝图用于规划 url
blue = Blueprint('book',__name__) # __name__表示导入到当前模块

# 使用缓存
@blue.route('/')
@cache.cached(timeout=20) # 给视图函数加上 20 秒的缓存。第一次打开要等5秒，20s内再打开是秒开的，因为有缓存。
def index():
    print('index111')
    time.sleep(5)
    print('Index')
    return 'index'


# '''切面编程'''———————————————————————————————————————————————————

# 中间件函数
# 每一次请求之前都会使用。
@blue.before_request
def before():
    print('before request.')
    
    print(request.path)
    print(request.method)
    print(request.remote_addr) # 客户端的IP
    
    # # 简单反爬
    # # 识别出爬虫 python-requests/2.31.0
    # # 正常浏览器访问  Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit...
    # print(request.user_agent) 
    # if 'python' in request.user_agent.string:
    #     return '检查出爬虫。' # 这个会被返回到爬虫运行者那边。
    
    # 针对IP做反爬  简单
    ip = request.remote_addr
    # 获取到了IP，如果这个IP之前就有缓存，直接返回 拦截。
    if cache.get(ip):
        # 做好拦截，进入视图函数。
        return 'please stop 爬虫!!!'
    else:
        # 如果这个IP之前没有缓存，对每个IP设置缓存，1秒内不能多次重复访问。
        cache.set(ip,'value',timeout=1)
    
    # '''切面编程'''—————————————————————————————————————————————————
```

spider.py

```python
import requests 

res = requests.get('http://127.0.0.1:5000/')
print(res.text) 

for i in range(10):  # 故意测试一下。
    res = requests.get('http://127.0.0.1:5000/')
    print(res.text)
```



# 前后端分离  

```python
from flask import Blueprint,render_template,jsonify,request 
from .models import *

# blue
blue = Blueprint('user',__name__)

@blue.route('/')
def index():
    return 'index'

# 前后端不分离
#       render_template('index.html',users=users)
# 前后端分离
#       后端返回 json 字符串  jsonify():
#       前端使用 ajax 来请求数据  ajax



@blue.route('/usres/',methods=['GET','POST','PUT','DELETE']) # 查 增 改 删
def users():
    if request.method == 'GET':
        return jsonify({'method':'GET'})
    elif request.method == 'POST':
        return jsonify({'method':'POST'})
    elif request.method == 'PUT':
        return jsonify({'method':'PUT'})
    elif request.method == 'DELETE':
        return jsonify({'method':'DELETE'})
```



# Flask类视图和RESTful

​		我们删除 view模块，新建一个 apis.py模块，对 上面的代码 修改，使用 api接口开发。
之前用路由修饰器来访问到视图函数，现在该用单独的一个urls.py模块（路游文件）来实现访问视图函数。 restful 封装了查 增 改 删

![截屏2024-09-10 14.50.54](flask_study.assets/%E6%88%AA%E5%B1%8F2024-09-10%2014.50.54.jpg)

先说一下 思路，就是在 exts模块中引入restful包的api，然后

```python


```

