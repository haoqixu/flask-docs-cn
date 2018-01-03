.. _tutorial-folders:

步骤 0: 创建目录
============================

在我们真正开始之前，让我们创建这个应用所需的目录::

    /flaskr
        /flaskr
            /static
            /templates

这个应用会以 Python 程序包的形式安装运行。这是安装和运行 Flask 应用的
推荐方法。在本教程的后文你会了解到如何运行 ``flaskr`` 。现在继续操作并
创建这个应用的目录结构。在接下来的步骤中，你会相继创建数据库模式和主模块。

简单地提一下，:file:`static` 目录下是用户可以通过 `HTTP` 访问的文件，
该目录用于存放 css 和 javascript 文件。Flask 会在 `templates` 目录下寻
找 `Jinja2`_ 模板。你在之后会看到相应的例子。

继续阅读 :ref:`tutorial-schema` 。

.. _Jinja2: http://jinja.pocoo.org
