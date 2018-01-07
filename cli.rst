.. _cli:

命令行接口 
======================

.. versionadded:: 0.11

.. currentmodule:: flask

Flask 0.11 其中一项友好的新功能就是对 `click <http://click.pocoo.org/>`_
命令行接口的内建集成。这使得大量的新特性在 Flask 生态以及你的应用程序中
成为可能。

基础使用
-----------

现在，在安装完 Flask 之后，你会在你的 virtualenv 中找到 :command:`flask` 脚本。
如果你不想安装 Flask 或者你有一个特殊的需求，你可以也使用 ``python -m flask``
实现相同的功能。

这个脚本提供对应用程序 :attr:`Flask.cli` 实例所有命令和一些内建命令进行访问的能力。
Flask 扩展也可以根据需要注册更多的命令。

:command:`flask` 脚本要能正常工作，需要发现应用程序。这是通过 ``FLASK_APP`` 环境
变量实现的。它可以设置成导入路径或者包含 Flask 应用的 Python 模块文件名。

在导入的文件中，应用的名字得是 ``app`` 或者是可选的冒号形式。举例来说，
``mymodule:application`` 会指明使用 :file:`mymodule.py` 文件里的 `application`
对象。

考虑一个例子，:file:`hello.py` 文件中包含一个名为 ``app`` 的应用。
下面我们运行它::

环境变量（在 Windows 下使用 ``set`` 替换 ``export`` ）::

    export FLASK_APP=hello
    flask run

或者使用文件名::

    export FLASK_APP=/path/to/hello.py
    flask run

Virtualenv 集成
----------------------

如果你一直使用 virtualenv，你可以把 ``export FLASK_APP`` 加到 ``activate``
脚本的最底下。这样每次你激活你的 virtualenv 同时也自动设置了正确的应用名字。

调试标志
----------

我们可以通过导出 ``FLASK_DEBUG`` 为 ``1`` 或者 ``0`` ，来控制
:command:`flask` 脚本自动开启或关闭应用程序的调试模式::

    export FLASK_DEBUG=1

运行一个 Shell
---------------

要运行一个交互式 Python shell，你可以使用 ``shell`` 命令::

    flask shell

它会启动一个交互式 Python shell，设置正确的应用上下文和本地变量。这是
通过调用应用的 :meth:`Flask.make_shell_context` 方法实现的。默认情况下，
你可以访问你的 ``app`` 和 :data:`g` 。

自定义命令
---------------

给 shell 添加命令十分简单。Flask 使用 `click`_ 作为命令接口，这使得自定义
命令的创建变得简单。例如，如果你想添加一个初始化数据库的命令，你可以这么
做::

    import click
    from flask import Flask

    app = Flask(__name__)

    @app.cli.command()
    def initdb():
        """Initialize the database."""
        click.echo('Init the db')

然后这个命令会出现在命令行上::

    $ flask initdb
    Init the db

应用上下文
-------------------

大多数命令在应用上进行操作，所以设置好应用上下文非常有用。因此，如果你在
``app.cli`` 上用 :meth:`~flask.cli.AppGroup.command` 注册一个回调函数，
回调函数会自动地被 :func:`cli.with_appcontext` 包装，这会告知命令行接口系统
保证应用上下文被正确创建。如果命令是后来用 :func:`~click.Group.add_command` 或者
其它方式添加的，应用上下文就不会自动创建。

通过传递 ``with_appcontext=False`` 参数给装饰器能够禁用它::

    @app.cli.command(with_appcontext=False)
    def example():
        pass

工厂函数
-----------------

如果你通过工厂函数创建你的应用（ :ref:`app-factories` ），你会发现 :command:`flask`
命令无法直接工作。Flask 自身无法找到创建应用实例的合适方法。因此，建议创建一个
独立的文件来实例化应用程序。这不是唯一的方法。另一个方式是通过 :ref:`custom-scripts`
支持。

例如，如果你有一个从文件名创建应用的工厂函数，你可以创建一个独立的文件
来从根据环境变量创建这样一个应用实例。

可以是一个名为 :file:`autoapp.py` 的文件，包含下列内容::

    import os
    from yourapplication import create_app
    app = create_app(os.environ['YOURAPPLICATION_CONFIG'])

这么做之后，你可以让 :command:`flask` 命令自动使用它::

    export YOURAPPLICATION_CONFIG=/path/to/config.cfg
    export FLASK_APP=/path/to/autoapp.py

在这之后， :command:`flask` 就能找到你的应用。

.. _custom-scripts:

自定义脚本
--------------

尽管最通用的方法是使用 :command:`flask` 命令，你也可以创建你自己的“驱动脚本”。
因为 Flask 使用 click 创建脚本，所以你当然也可以把你的脚本绑定到任何 click 应用上。
得郑重提醒你，注册到 :attr:`Flask.cli` 的命令得（至少是间接地）从
:class:`flask.cli.FlaskGroup` click 组启动。这非常必要，因为这样命令才能知道
它们操作的是哪一个 Flask 应用。

要理解为什么你可能想要使用自定义脚本，你需要理解 click 是如何找到并执行 Flask
应用的。如果你使用 :command:`flask` 脚本，你通过命令行或者环境变量指定应用的
导入名称。尽管这很简单，但是它有所限制。首先，它无法和应用工厂函数一起工作
（ 查阅 :ref:`app-factories` ）。

通过自定义脚本，你避免了这个问题，因为你可以完全自定义应用创建的方式。
如果你想编写可重用的应用，并把它随同自定义管理脚本发布给用户，
自定义脚本将非常有用。

为了解释整个过程，这里以 :file:`manage.py` 脚本为例，它管理一个假像的维基
应用。下面我们讨论它的细节::

    import os
    import click
    from flask.cli import FlaskGroup

    def create_wiki_app(info):
        from yourwiki import create_app
        return create_app(
            config=os.environ.get('WIKI_CONFIG', 'wikiconfig.py'))

    @click.group(cls=FlaskGroup, create_app=create_wiki_app)
    def cli():
        """This is a management script for the wiki application."""

    if __name__ == '__main__':
        cli()

这代码看起来很多实际不多，让我们一步步理解它。

1.  首先，我们导入 ``click`` 代码库以及 ``flask.cli`` 包中的 click 扩展。
    这里我们感兴趣的是 :class:`~flask.cli.FlaskGroup` click 组。
2.  接下来，我们定义一个接受来自 Flask 的脚本 info 对象
    （ :class:`~flask.cli.ScriptInfo` ）作为参数的函数。他的目的是导入并创建
    应用。这可以通过直接导入一个现成的应用对象或者创建一个
    （查看 :ref:`app-factories` ）来实现。在这里我们从环境变量中加载配置。
3.  下一步是创建一个 :class:`FlaskGroup` 。这里我们创建一个仅仅具有帮助文档
    字符串的空函数，然后把 ``create_wiki_app`` 函数作为工厂函数。

    任何时候只有 click 需要在一个 Flask 应用上操作，它就会利用脚本 info 调用函数
    请求创建应用。
4.  调用脚本，所有的功能都被整合好。

命令行插件
-----------

如果需要，Flask 扩展总是可以修补 :attr:`Flask.cli` 实例来支持更多命令。
然而，有另一种给 Flask 添加命令行插件的方法，就是通过 ``setuptools`` 。
如果如果你的 Python 包需要导出一个 Flask 命令行插件，你可以修改 :file:`setup.py`
文件，声明一个指向 click 命令的入口。

例子 :file:`setup.py`::

    from setuptools import setup

    setup(
        name='flask-my-extension',
        ...
        entry_points='''
            [flask.commands]
            my-command=mypackage.commands:cli
        ''',
    )

在 :file:`mypackage/commands.py` 内你可以导出一个 Click 对象::

    import click

    @click.command()
    def cli():
        """This is an example command."""

一旦把包安装到与 Flask 相同的 virtualenv 下，你就可以运行 ``flask my-command``
调用你的命令了。这在提供 Flask 本身没有的额外功能时很有用。
