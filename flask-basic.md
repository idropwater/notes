# flask 基础  

## 启动

``` 
# 添加环境变量并启动程序
$ export FLASK_APP=<entry_filename>
$ flask run
* Running on http://127.0.0.1:5000/
```

```
# 设置开发环境
$ export FLASK_ENV=development
$ flask run
```

## 常见模块和包
- [watchdog](https://pythonhosted.org/watchdog/ )
- [unittest](https://docs.python.org/2/library/unittest.html)
- [blueprint](http://dormousehole.readthedocs.io/en/latest/blueprints.html)
- [ flask-restful](https://github.com/flask-restful/flask-restful)
- [ flask-bootstrap](http://flask-bootstrap-zh.readthedocs.io/zh/latest/)
- [ last-moment](https://github.com/miguelgrinberg/Flask-Moment) 
- [python-dotenv](https://github.com/theskumar/python-dotenv) 使用.env 文件统一管理环境变量，key=value
- [virtualenv](https://virtualenv.pypa.io/en/stable/) 虚拟环境
- [ Flask-SQLAlchemy](http://flask-sqlalchemy.pocoo.org/2.3/)  Flask ORM
- [ Flask-Migrate](http://flask-migrate.readthedocs.io/en/latest/) 数据库迁移
- [ flask-login](https://github.com/maxcountryman/flask-login)   Flask user session 管理
- [ Flask-WTF](https://github.com/lepture/flask-wtf)  提供强大的Form表单安全和校验机制
- [ flask-babel](http://babel.pocoo.org/en/latest/ ) Flask国际化和本地化扩展
- [ Flask-Login](http://flask-login.readthedocs.io/en/latest/) 认证用户状态
- [ flask-script](http://flask-script.readthedocs.io/en/latest/) 可以为flask 程序添加命令行解释器
- [ flask-mail](https://pythonhosted.org/Flask-Mail/) 将电子邮件发送给用户
- [Werkzeug](http://werkzeug.pocoo.org/) python WSGI库 web服务器网关接口
- [Jinja](http://jinja.pocoo.org/) 模版引擎
- [MarkupSafe](https://pypi.org/project/MarkupSafe/) 避免注入攻击 escape
- [itsdangerous](https://pythonhosted.org/itsdangerous/) 安全
- [Click](http://click.pocoo.org/5/) 快速写命令行工具

## restful api
- uri统一资源定位符，无状态
- crud -> http：get／put／post／delete／patch等方法
- 认证机制 session auth、basic auth、token auth、OAuth

### 序列化与反序列化
- json
- xml
- [protobuf](https://developers.google.com/protocol-buffers/)
- [graphql](http://graphql.cn/)

### 校验
- 数据类型校验
- 数据格式校验
- 数据逻辑校验

### 用户认证、权限机制
[oAuth 行业标准](https://oauth.net/2/)

### 跨域
阮一峰老师[跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)

### URL的规范
阮一峰老师[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

### 测试工具
测试API可用工具[Postman](https://www.getpostman.com/)


## 常见问题

### 请求上下文
```
from flask import request

@app.route(‘/‘)
def index():
    user_agent = request.header.get(‘User-Agent’)
    retur user_agent
```

- request 请求对象，请求上下文，在一个线程中全局访问
- session 请求上下文，用与存储请求之间需要记住的值的词典
- current_app 程序上下文，当前激活程序的程序实例
- g 程序上下文，处于请求时临时存储的对象


### 自定义错误页面

```
@app.errorhanlder(400)
def page_not_found(e)
    return render_tempalte(‘404.html’), 404
```

### 数据库迁移

开发过程中，经常会修改数据库模型，修改之后要更新数据库
更新表的唯一方式就是先删除旧表，这样做会丢失数据库中的所有数据

更新表的更好方法是采用数据迁移框架
[flask-migrate](http://flask-migrate.readthedocs.io/en/latest/)

### 大型程序的结构
```
—app                    flask 程序
—— templates            存放模版文件
—— static               存放静态文件
—— main
—— ...
—migrations             数据库迁移脚本
-tests                  测试
-venv                   虚拟环境
-requirements.txt       依赖
-config.py              存储配置，例如开发、测试以及生产环境不同配置需求
-manage.py              用于启动程序以及其他的程序任务
-.env                   保存环境便利 key=value
```

### 蓝本Blueprin，实现模块化组织程序结构

何为蓝本？为什么需要蓝本？
[Blueprin](http://dormousehole.readthedocs.io/en/latest/blueprints.html 
)





























