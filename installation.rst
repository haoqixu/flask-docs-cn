.. _installation:

安装
============

Flask 依赖一些外部库，像 `Werkzeug
<http://werkzeug.pocoo.org/>`_ 和 `Jinja2 <http://jinja.pocoo.org/>`_ 。
Werkzeug 是一个 WSGI（在 Web 应用和多种服务器之间的标准 Python 接口) 工具
集。Jinja2 负责渲染模板。

那么如何在你的电脑上安装这一切？虽说条条大道通罗马，但是最强大的方式是
virtualenv ，所以我们首先来看它。

你首先需要 Python 2.6 或更新的版本，所以请确认有一个最新的 Python 2.x 安装。
在 Python 3 中使用 Flask 请参考： :ref:`python3-support` 。

.. _virtualenv:

virtualenv
----------

你很可能想在开发中用上 virtualenv，如果你有生产环境的 shell 权限，你同样
会乐于在生产环境中使用它。

virtualenv 解决了什么问题？如果你像我一样喜欢 Python，不仅会在采用 Flask
的Web 应用中用上 virtualenv，在别的项目中你也会想用上它。你拥有的项目越
多，同时使用不同版本的 Python 工作的可能性也就越大，或者起码需要不同版本
的 Python 库。悲惨现实是：常常会有库破坏向后兼容性，然而正经应用不采用外
部库的可能微乎其微。当在你的项目中，出现两个或更多依赖性冲突时，你会怎么
做？

virtualenv 拯救世界！virtualenv 为每个不同项目提供一份 Python 安装。它并
没有真正安装多个 Python 副本，但是它确实提供了一种巧妙的方式来让各项目环
境保持独立。让我们来看看 virtualenv 是怎么工作的。

如果你在 Mac OS X 或 Linux 下，下面的命令可能会适用::

    $ sudo pip install virtualenv

这条命令会在你的系统中安装 virtualenv。实际上，你的包管理器可能也包含了这个工具。
如果你用的是 Ubuntu，可以尝试::

    $ sudo apt-get install python-virtualenv

如果你用的是 Windows ，而且没有 ``easy_install`` 命令，那么你必须先安装这
个命令。查阅 :ref:`windows-easy-install` 章节了解如何安装。之后，运行上
述的命令，但是要去掉 ``sudo`` 前缀。

virtualenv 安装完毕后，你可以立即打开 shell 然后创建你自己的环境。我通
常创建一个项目文件夹，并在其下创建一个 :file:`venv` 文件夹 ::

    $ mkdir myproject
    $ cd myproject
    $ virtualenv venv
    New python executable in venv/bin/python
    Installing setuptools, pip............done.

现在，无论何时你想在某个项目上工作，只需要激活相应的环境。在 OS X 和
Linux 上，执行如下操作::

    $ . venv/bin/activate

下面的操作适用 Windows::

    $ venv\Scripts\activate

无论通过哪种方式，你现在应该已经激活了 virtualenv（注意你的 shell 提示符
显示的是当前活动的环境）。

如果你想回到真实的环境中，使用下面这个命令::

    $ deactivate

运行后，你的 shell 命令提示符应该会变回之前那样。

现在，我们继续运行下面的指令，让 virtualenv 环境下的 Flask 就绪::

    $ pip install Flask

几秒之后，一切都就绪了。


.. _system-wide-installation:

全局安装
------------------------

这样也是可以的，虽然我不推荐。只需要以 root 权限运行 ``pip``::

    $ sudo pip install Flask

（在 Windows 上，在管理员权限的命令提示符中去掉 ``sudo`` 运行这条命令 。）

.. _living-on-the-edge:

活在前沿
------------------

如果你需要最新版本的 Flask，有两种方法：你可以使用 ``pip`` 拉取开发版本，
或让它操作一个 git checkout。无论哪种方式，依然推荐使用 virtualenv。

在一个全新的 virtualenv 中 git checkout 并运行在开发模式下::

    $ git clone http://github.com/pallets/flask.git
    Initialized empty Git repository in ~/dev/flask/.git/
    $ cd flask
    $ virtualenv venv
    New python executable in venv/bin/python
    Installing setuptools, pip............done.
    $ . venv/bin/activate
    $ python setup.py develop
    ...
    Finished processing dependencies for Flask

这会拉取依赖并激活 git head 作为 virtualenv 中的当前版本。然后你只需要执
行 ``git pull origin`` 来升级到最新版本。

.. _windows-easy-install:

Windows 下的 `pip` 和 `setuptools`
-----------------------------------

在 Windows 下，像 ``pip``、``setuptools``、``virtualenv`` 这样的标准 Python 打包工具安装起来有时会稍显麻烦，但还是相当简单。
你所需的最关键的包是 pip，它能帮你安装其它任何东西（比如 virtualenv）。幸运的是，你可以通过一个辅助脚本安装它。

如果你还未安装 ``pip`` ， `get-pip.py` 能协助你完成。

`get-pip.py`_

下载完成后，你只需要双击运行就能完成 ``pip`` 的安装。

你可以运行下面指令更新它们::

    > pip install --upgrade pip setuptools

很多时候，你打开一个终端，想要直接输入 ``pip`` 和 ``python`` 来运行它们。
但是在 Windows 上这或许会不可行，因为它不知道这些可执行文件在哪（你可以试一试！）。

要解决这个问题，你得能够访问到你的 Python 安装目录（比如 :file:`C:\Python27` ），然后
进入 :file:`Tools` ，再是 :file:`Scripts` ，找到并且运行 :file:`win_add2path.py` 文件。
打开一个 **新的** 命令行终端，检查一下现在是否能够通过直接输入 ``python`` 打开解释器。

最后，为了安装 `virtualenv`_ ，你可以简单的运行::

    > pip install virtualenv

然后你就可以开始根据上面提到的安装指令执行了。

.. _get-pip.py: https://bootstrap.pypa.io/get-pip.py
