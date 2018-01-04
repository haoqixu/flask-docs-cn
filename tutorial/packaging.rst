.. _tutorial-packaging:

步骤 3: 以包的形式安装 flaskr
======================================

Flask 现在已经集成了 `Click`_ 。Click 为 Flask 增强和扩展了命令行功能。
在本教程后文中，你将看到如何扩展 ``flask`` 命令行接口（CLI）。

按照 `Python Packaging Guide`_ 的描述安装应用是一种管理 Flask 应用程序的实用模式。
这种模式的引入需在根目录下创建 :file:`setup.py` 和 :file:`MANIFEST.in` 两个文件。
你还需要添加 :file:`__init__.py` 文件使得 :file:`flaskr/flaskr` 目录成为一个包。
一番修改过后，你的代码结构应该如下::

    /flaskr
        /flaskr
            __init__.py
            /static
            /templates
            flaskr.py
            schema.sql
        setup.py
        MANIFEST.in

``flaskr`` 的 ``setup.py`` 内容：

.. sourcecode:: python

    from setuptools import setup

    setup(
        name='flaskr',
        packages=['flaskr'],
        include_package_data=True,
        install_requires=[
            'flask',
        ],
    )

在使用 setuptools 时，它需要你（在 :file:`MANIFEST.in` 中）指定包中所包含的所有特殊文件。
在这个实例中，静态文件、模板和模式文件需要被包含。创建 :file:`MANIFEST.in` 文件并添加
下面几行::

    graft flaskr/templates
    graft flaskr/static
    include flaskr/schema.sql

为了简化应用实例的定位，在 :file:`flaskr/__init__.py` 文件添加下面的
导入语句:

.. sourcecode:: python

    from .flaskr import app

这个导入语句把应用程序的实例带到了应用程序包的顶层。在运行应用的时候，Flask
开发服务器需要应用实例的位置。这个导入语句简化了定位过程。少了它，之后几步中的
导出语句需要改为::
``export FLASK_APP=flaskr.flaskr``

这是你应该能够安装这个应用了。像往常一样，建议你在一个 `virtualenv`_ 环境下
安装你的 Flask 应用。说了这么多，下面让我们继续安装这个应用::

    pip install --editable 

上面这条安装指令是在项目的根目录下执行的。 `editable` 选项使得你无须在每次修改代码之后
重新安装 Flask 应用。现在，flaskr 应用已经在你的 virtualenv 环境下安装好了
（可以查看 ``pip freeze`` 的输出进行确认）。

解决了上面的问题，你现在应该能够启动应用了。使用下面的命令::

    export FLASK_APP=flaskr
    export FLASK_DEBUG=true
    flask run

（在 Windows 下，你需要用在 Windows 下，你需要用 `set` 替换 `export` 。）

:envvar:`FLASK_DEBUG` 标志关系开启或者禁用交互式调试器。
*永远不要在生产系统中激活调试模式* ， 因为它将允许用户在服务器上执行代码。

你将会看见有消息告诉你访问该服务器的地址。

当你在浏览器中访问服务器遇到一个 404 page not found 错误时，
是因为我们还没有任何视图。我们之后再来关注视图。首先我们应该让数
据库工作起来。

.. admonition:: 外部可见的服务器

   想要你的服务器公开可见？
    :ref:`外部可见的服务器 <public-server>` 一节有更多信息。

继续阅读 :ref:`tutorial-dbinit` 。

.. _Click: http://click.pocoo.org
.. _Python Packaging Guide: https://packaging.python.org
.. _virtualenv: https://virtualenv.pypa.io
