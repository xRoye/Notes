## 关于Flask

Ref. https://flask.github.net.cn/quickstart.html

Flask是一个轻量级的Web框架，它是由Armin Ronacher开发的，使用Python语言编写。Flask易于上手，具有高度的可扩展性，非常适合快速开发Web应用程序。

Flask是一个基于Werkzeug WSGI工具箱和Jinja2模板引擎的Web框架。与Django等其他Web框架相比，Flask更加轻量级，它没有内置的ORM、表单验证等功能，而是通过扩展来实现这些功能。这使得Flask更加灵活，可以根据项目的需求选择合适的扩展。

## install

pip install Flask

## Hello World !

一个最小的 Flask 应用如下:

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'
```

那么，这些代码是什么意思呢？

1. 首先我们导入了 Flask 类。 该类的实例将会成为我们的 WSGI 应用。

2. 接着我们创建一个该类的实例。第一个参数是应用模块或者包的名称。如果你使用 一个单一模块（就像本例），那么应当使用 \_\_name__ ，因为名称会根据这个 模块是按应用方式使用还是作为一个模块导入而发生变化（可能是 '\_\_main__' ， 也可能是实际导入的名称）。这个参数是必需的，这样 Flask 才能知道在哪里可以 找到模板和静态文件等东西。更多内容详见 Flask 文档。

3. 然后我们使用 route() 装饰器来告诉 Flask 触发函数的 URL 。

4. 函数名称被用于生成相关联的 URL 。函数最后返回需要在用户浏览器中显示的信息。

把它保存为 hello.py 或其他类似名称。请不要使用 flask.py 作为应用名称，这会与 Flask 本身发生冲突。

可以使用 flask 命令或者 python 的 -m 开关来运行这个应用。在 运行应用之前，需要在终端里导出 FLASK_APP 环境变量:

```
$ export FLASK_APP=hello.py
$ flask run
 * Running on http://127.0.0.1:5000/
```

如果是在 Windows 下，那么导出环境变量的语法取决于使用的是哪种命令行解释器。 在 Command Prompt 下:

`C:\path\to\app>set FLASK_APP=hello.py`

在 PowerShell 下:

`PS C:\path\to\app> $env:FLASK_APP = "hello.py"`

还可以使用 python -m flask:

```
$ export FLASK_APP=hello.py
$ python -m flask run
 * Running on http://127.0.0.1:5000/
```

这样就启动了一个非常简单的内建的服务器。这个服务器用于测试应该是足够了，但是 用于生产可能是不够的。关于部署的有关内容参见《 部署方式 》。

现在在浏览器中打开 http://127.0.0.1:5000/ ，应该可以看到 Hello World! 字样。

```
外部可见的服务器
运行服务器后，会发现只有你自己的电脑可以使用服务，而网络中的其他电脑却 不行。缺省设置就是这样的，因为在调试模式下该应用的用户可以执行你电脑中 的任意 Python 代码。

如果你关闭了调试器或信任你网络中的用户，那么可以让服务器被公开访问。 只要在命令行上简单的加上 --host=0.0.0.0 即可:

$ flask run --host=0.0.0.0
这行代码告诉你的操作系统监听所有公开的 IP 。
```


## 调试模式

如果你打开 调试模式，那么服务器会在修改应用代码之后自动重启，并且当应用出错时还会提供一个 有用的调试器。

如果需要打开所有开发功能（包括调试模式），那么要在运行服务器之前导出 FLASK_ENV 环境变量并把其设置为 development:

```
$ export FLASK_ENV=development
$ flask run
```
（在 Windows 下需要使用 set 来代替 export 。）

这样可以实现以下功能：

1. 激活调试器。

2. 激活自动重载。

3. 打开 Flask 应用的调试模式。

还可以通过导出 FLASK_DEBUG=1 来单独控制调试模式的开关。