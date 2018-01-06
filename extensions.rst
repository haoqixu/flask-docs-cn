Flask 扩展
================

Flask 扩展用多种不同的方式扩充 Flask 的功能。比如加入数据库支持和其它的
常见任务。

寻找扩展
------------------

`Flask Extension Registry`_ 中列出了 Flask 扩展，并且可以通过
:command:`easy_install` 或 :command:`pip` 下载。如果你把一个 Flask 扩展添加到
:file:`requirements.rst` 或 :file:`setup.py` 文件的依赖关系中，它们通常可以用一个
简单的命令或是在你应用安装时被安装。


使用扩展
----------------

扩展通常附带有文档，来展示如何使用它。扩展的行为没有一个可以预测的一般性
规则，除了它们是从同一个位置导入的。如果你有一个名为 ``Flask-Foo`` 或是
``Foo-Flask`` 的扩展，你可以从 ``flask_foo`` 导入它::

    import flask_foo

创建扩展
----------------

尽管 `Flask Extension Registry`_ 包含许多 Flask 扩展，但你可能找不到满足你
需求的。遇到这种状况，你可以创建你自己的扩展。考虑开发自己的 Flask 扩展，
可以阅读 :ref:`extension-dev` 章节。

Flask 0.8 以前
----------------

如果你在使用 Flask 0.7 或更早的版本，包 :data:`flask.ext` 并不存在，你不得不
从 ``flaskext.foo`` 或 ``flask_foo`` 中导入，这取决与应用是如何分发的。如果你
想要开发支持 Flask 0.7 或更早版本的应用，你仍然应该从 :data:`flask.ext` 中导
入。我们提供了一个兼容性模块来在 Flask 的老版本中提供这个包。你可以从 GitHub
上下载它：`flaskext_compat.py`_

这里是使用它的方法::

    import flaskext_compat
    flaskext_compat.activate()

    from flask.ext import foo

一旦激活了 ``flaskext_compat`` 模块，就会存在 :data:`flask.ext` ，并且你可以从
那里开始导入。

.. _Flask Extension Registry: http://flask.pocoo.org/extensions/
.. _flaskext_compat.py: https://raw.githubusercontent.com/pallets/flask/master/scripts/flaskext_compat.py
