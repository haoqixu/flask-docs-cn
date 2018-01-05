.. _tutorial-dbinit:

步骤 5: 创建数据库
=============================

正如之前介绍的，Flaskr 是一个数据库驱动的应用，更准确的说法
是，一个由关系数据库系统驱动的应用。关系数据库系统需要一个模
式来决定存储信息的方式。所以在第一次开启服务器之前，要点是创
建模式。

可以通过管道把 `schema.sql` 作为 `sqlite3` 命令的输入来创建这
个模式，命令为如下::

    sqlite3 /tmp/flaskr.db < schema.sql

这种方法的缺点是需要安装 sqlite3 命令，而并不是每个系统都有安
装。而且你必须提供数据库的路径，这个可能会引入错误。用函数来初始化数据
库是个不错的想法。

我们可以创建一个函数并且把它接入 :command:`flask` 命令用于初始化数据库。
现在让我们看看下面的代码段。把这个函数放在 :file:`flaskr.py` 里
`connect_db` 函数的后面::

    def init_db():
        db = get_db()
        with app.open_resource('schema.sql', mode='r') as f:
            db.cursor().executescript(f.read())
        db.commit()

    @app.cli.command('initdb')
    def initdb_command():
        """Initializes the database."""
        init_db()
        print('Initialized the database.')

``app.cli.command()`` 装饰器注册了一个 :command:`flask` 脚本的
新命令。当指令执行时，Flask 会自动创建一个与应用绑定的应用环境。
所以，在函数中你可以访问 :attr:`flask.g` 以及其它你希望访问的东西。
当脚本运行结束，应用会销毁应用环境并且释放数据库连接。

你还是会希望保留一个初始化数据库的实际函数，这样我们可以在后面的单元测试中
方便地创建数据库。（更多的信息请查阅 :ref:`testing` ）

应用对象的 :func:`~flask.Flask.open_resource` 方法是一个很方便
的辅助函数，可以打开应用提供的资源。这个函数从资源所在位置（
你的 :file:`flaskr/flaskr` 文件夹）打开文件，并允许你读取它。我们在此用它来
在数据库连接上执行脚本。

SQLite 的数据库连接对象提供了一个游标对象。游标上有一个方法可
以执行完整的脚本。最后我们只需提交变更。SQLite 3 和其它支持事
务的数据库只会在你显式提交的时候提交。

现在可以通过 :command:`flask` 命令来创建数据库::

    flask initdb
    Initialized the database.

.. admonition:: 故障排除

   如果你遇到了表无法找到的异常，请检查你是否确实调用过
   `init_db` 函数并且表的名称是正确的（比如弄混了单数和复数）。


继续阅读 :ref:`tutorial-views` 。
