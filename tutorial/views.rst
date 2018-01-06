.. _tutorial-views:

步骤 6: 视图函数
==========================

现在数据库连接已经正常工作，你可以开始写视图函数了。
你总共需要写四个视图函数:

显示条目
------------

这个视图显示数据库中存储的所有条目。它绑定在应用的根地址，并从数据库查询出
文章的标题和正文。id 值最大的条目（最新的条目）会显示在最上方。从指针返回的
行是看起来像字典，因为我们使用了 :class:`sqlite3.Row` 行工厂函数。

视图函数会将条目传递给 :file:`show_entries.html` 模板，并返回渲染结果::

    @app.route('/')
    def show_entries():
        db = get_db()
        cur = db.execute('select title, text from entries order by id desc')
        entries = cur.fetchall()
        return render_template('show_entries.html', entries=entries)

添加条目
-------------

这个视图允许已登入的用户添加新条目，并只响应 ``POST`` 请求，实际的表单显示在
`show_entries` 页。如果一切工作正常，它会用 :func:`~flask.flash`  向下
一次请求发送提示消息，并重定向回 `show_entries` 页::

    @app.route('/add', methods=['POST'])
    def add_entry():
        if not session.get('logged_in'):
            abort(401)
        db = get_db()
        db.execute('insert into entries (title, text) values (?, ?)',
                     [request.form['title'], request.form['text']])
        db.commit()
        flash('New entry was successfully posted')
        return redirect(url_for('show_entries'))

注意这里的用户登入检查（ `logged_in` 键在会话中存在，并且为 `True` ）

.. admonition:: 安全提示

   确保像上面例子中一样，使用问号标记来构建 SQL 语句。否则，当你使用格式化
   字符串构建 SQL 语句时，你的应用容易遭受 SQL 注入。
   更多请见 :ref:`sqlite3` 。

登入和登出
----------------

这些函数用来让用户登入登出。登入通过与配置文件中的数据比较检查用户名和密码，
并设定会话中的 `logged_in` 键值。如果用户成功登入，那么这个键值会被设为
``True`` ，并跳转回 `show_entries` 页。此外，会有消息闪现来提示用户登入成功。
如果发生一个错误，模板会通知，并提示重新登录。

::

    @app.route('/login', methods=['GET', 'POST'])
    def login():
        error = None
        if request.method == 'POST':
            if request.form['username'] != app.config['USERNAME']:
                error = 'Invalid username'
            elif request.form['password'] != app.config['PASSWORD']:
                error = 'Invalid password'
            else:
                session['logged_in'] = True
                flash('You were logged in')
                return redirect(url_for('show_entries'))
        return render_template('login.html', error=error)

函数 `logout` ，做相反的事情，从会话中删除 `logged_in` 键。我们这里使用了一个
简洁的方法：如果你使用字典的 :meth:`~dict.pop` 方法并传入第二个参数（默认），
这个方法会从字典中删除这个键，如果这个键不存在则什么都不做。这很有用，因为
我们不需要检查用户是否已经登入。

::

    @app.route('/logout')
    def logout():
        session.pop('logged_in', None)
        flash('You were logged out')
        return redirect(url_for('show_entries'))

.. admonition:: 安全提示

    在生产环境中，永远不要用明文存储密码。本教程使用明文密码是为了简化。
    如果你打算对外发布一个基于该教程的项目，将密码存储到数据库或者文件前，
    切记对它进行 `加盐哈希`_ 操作。

    幸运的是，有一些插件用于对密码进行哈希以及验证哈希密码，所以添加这个功能
    也是非常直接了当的。也有很多通用的 Python 函数库可以用于哈希。

    你可以在 `这里 <http://flask.pocoo.org/extensions/>`_ 找到一系列推荐的
    Flask 扩展。


继续阅读 :ref:`tutorial-templates` 。

.. _加盐哈希: https://blog.codinghorror.com/youre-probably-storing-passwords-incorrectly/
