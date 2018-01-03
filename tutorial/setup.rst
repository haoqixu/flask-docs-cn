.. _tutorial-setup:

步骤 2: 应用设置代码
==============================

现在数据库模式就绪了，我们可以创建应用模块 :file:`flaskr.py` 了。
这个文件应该放置在 :file:`flaskr/flaskr` 目录下。应用模块的前几行代码
是所需的导入语句，接着是一些配置用的代码行。对于像 ``flaskr`` 这样的
小型应用，可以直接把配置放在主模块 里。但更简洁的方案是创建独立的 `.ini` 或
`.py` 文件，并载入或导入里面的值。

这是导入语句（在 :file:`flaskr.py` 中）::

    # all the imports
    import os
    import sqlite3
    from flask import Flask, request, session, g, redirect, url_for, abort, \
         render_template, flash

接下来的代码创建了实际的应用实例，并且使用同一文件（ :file:`flaskr.py` ）中的配置
初始化。

.. sourcecode:: python

    app = Flask(__name__) # create the application instance :)
    app.config.from_object(__name__) # load config from this file , flaskr.py

    # Load default config and override config from an environment variable
    app.config.update(dict(
        DATABASE=os.path.join(app.root_path, 'flaskr.db'),
        SECRET_KEY='development key',
        USERNAME='admin',
        PASSWORD='default'
    ))
    app.config.from_envvar('FLASKR_SETTINGS', silent=True)

:class:`~flask.Config` 对象的用法如同字典，所以我们可以用新值更新
它。

.. admonition:: 数据库路径

    操作系统有进程当前工作目录的概念。不幸的是，你在 Web 应用中不能
    依赖此概念，因为你可能会在相同的进程中运行多个应用。

    为此，提供了 ``app.root_path`` 属性以获取应用的路径。配合
    ``os.path`` 模块使用，轻松可达任意文件。在本例中，我们把数据库
    放在根目录下。

    对于实际生产环境的应用，推荐使用 :ref:`instance-folders` 。


通常，加载一个单独的、环境特定的配置文件是个好主意。Flask 允许你导
入多份配置，并且使用最后的导入中定义的设置。这使得配置设定过程更可
靠。 :meth:`~flask.Config.from_envvar` 可用于达此目的。

.. sourcecode:: python

    app.config.from_envvar('FLASKR_SETTINGS', silent=True)

只需设置一个名为 :envvar:`FLASKR_SETTINGS` 的环境变量，指向要加载的
配置文件。启用静默模式告诉 Flask 在没有设置该环境变量的情况下不发出警告。

此外，你可以使用配置对象上的 :meth:`~flask.Config.from_object` 方
法，并传递一个模块的导入名作为参数。Flask 会从这个模块初始化变量。
注意，只有名称全为大写字母的变量才会被采用。

``SECRET_KEY`` 是保证客户端会话的安全的要点。正确选择一个尽可能难猜
测、尽可能复杂的密钥。

我们还添加了一个让连接到指定数据库变得很简单的方法，这个方法用于
在请求时开启一个数据库连接，并且在交互式 Python shell 和脚本中
也能使用。这为以后的操作提供了相当的便利。我们创建了一个简单的
SQLite 数据库的连接，并让它用 :class:`sqlite3.Row` 表示数据库中
的行。这使得我们可以通过字典而不是元组的形式访问行::

.. sourcecode:: python

    def connect_db():
        """Connects to the specific database."""
        rv = sqlite3.connect(app.config['DATABASE'])
        rv.row_factory = sqlite3.Row
        return rv

在下一节，我们将看到如何运行应用程序。

继续阅读 :ref:`tutorial-packaging` 。
