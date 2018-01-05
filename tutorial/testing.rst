.. _tutorial-testing:

福利: 应用测试
==============================

现在你应该完成你的应用，并且一切都按预期运转正常，对于简化未来的修改，添加
自动测试不是一个坏主意。上面的应用将作为文档中 :ref:`testing` 节的例子来演示
如何进行单元测试。去看看测试 Flask 应用是多么简单的一件事。

为 flaskr 添加测试
--------------------------------

假如你已经读过 :ref:`testing` 章节，并且为 ``flaskr`` 编写自己的测试代码
或者跟随样例编写，你应该会组织项目的方式感到疑惑。

下面是一个可行并且推荐的项目结构::

    flaskr/
        flaskr/
            __init__.py
            static/
            templates/
        tests/
            test_flaskr.py
        setup.py
        MANIFEST.in

现在可以去创建 :file:`tests/` 目录和 :file:`test_flaskr.py` 文件了。

运行测试
----------------

现在你可以运行测试代码，这里会用到 ``pytest`` 。

.. note:: 确保 ``pytest`` 和 flaskr 安装在同一个 virtualenv 环境下。
    否则， ``pytest``  测试代码无法导入测试应用必要的组件。

        pip install -e .
        pip install pytest

在顶层 :file:`flaskr/` 目录下运行并观察测试通过::

    py.test

测试 + setuptools
--------------------------------

还有一种处理测试的方法，就是和 ``setuptools`` 集成。这里需要在 :file:`setup.py`
下添加一些代码，以及创建 :file:`setup.cfg` 新文件。这种运行测试的方法好处在于
你不用安装 ``pytest`` 。在 :file:`setup.py` 添加下面的代码::

    from setuptools import setup

    setup(
        name='flaskr',
        packages=['flaskr'],
        include_package_data=True,
        install_requires=[
            'flask',
        ],
        setup_requires=[
            'pytest-runner',
        ],
        tests_require=[
            'pytest',
        ],
    )

现在在根目录下创建 :file:`setup.cfg` （与 :file:`setup.py` 同在一个目录）::

    [aliases]
    test=pytest

现在你可以运行::

    python setup.py test

这会在 :file:`setup.py` 脚本被调用时请求 :file:`setup.cfg` 中创建的别名，
依次通过 ``python-runner`` 运行 ``pytest`` 。根据测试发现的标准规则，你的
测试代码会被找到并运行，幸运的话会通过。

这是运行和管理测试的一种可行方案。这里用到了 ``pytest`` 但是也能换成
其它像 ``nose`` 的框架。与 ``setuptools`` 集成会便捷些，因为它不需要下载
``pytest`` 或者其它可能用到的测试框架。
