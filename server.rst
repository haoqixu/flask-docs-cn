.. _server:

开发服务器
==================

.. currentmodule:: flask

自 Flask 0.11 起，有多种内建方式运行开发服务器。最佳方式是使用 :command:`flask`
命令行工具，但是你也可以继续使用 :meth:`Flask.run` 方法。

通过命令行
------------

强烈推荐在开发时使用 :command:`flask` 命令行脚本（ :ref:`cli` ），得益于加载应用的方式，
它能提供极佳的重加载体验。基本用法如下::

    $ export FLASK_APP=my_application
    $ export FLASK_DEBUG=1
    $ flask run

它启用了调试器、重载器，然后在 *http://localhost:5000/* 地址上运行了服务器。

服务器独立的特性能通过传递给 ``run`` 选项的参数加以控制。例如，重载器能
够被禁用::

    $ flask run --no-reload

通过代码
-------

一个运行应用的替代方式是使用 :meth:`Flask.run` 方法。这会像 :command:`flask`
脚本一样立即启动一个本地服务器。

例子::

    if __name__ == '__main__':
        app.run()

在大多数情况下，这能很好地工作，但是它不适用于开发。也正因如此，自 Flask 0.11 起
推荐使用 :command:`flask` 脚本。之所以不适用的原因是由重加载的机制引起的，它可能会出现一些
诡异的副作用（像重复执行部分代码两次，有时无故崩溃或者伴随语法或导入错误崩溃）。

然而，在不需要自动重加载的情况下，这依旧是个启动应用完美有效的方法。
