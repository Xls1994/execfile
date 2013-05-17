名称空间和execfile函数
===============================================

名称空间
----------------------
1. 名称空间就是一个 字典。 key 是变量名， value 是变量的值。
2. 每个模块有名称空间，每个函数有名称空间。
   
globals() 和 locals()   
----------------------
1. globals() 返回当前的全局名称空间， locals() 返回当前的局部名称空间。

.. code-block:: python

   # namespace.py
   x='global var'
   print 'x' in locals()
   print 'x' in globals()
   def fun():
       y='local var'
       print 'y' in globals()
       print 'y' in locals()
       print 'x' in globals()
       print 'x' in locals()

   fun()

::

   results:
       True
       True
       False
       True
       True
       False

2. 在module最顶层 locals() 与 globals() 是相同的， module的函数里， globals() 与module顶层的 globals() 相同，locals() 里是函数里新建的局部变量。


execfile() 函数
--------------------------
1. 用法： execfile(filname, globals=globals(), locals=locals()) 

   * globals 和 locals 是可选的，如果不指定的话，就是用默认的执行环境的名称空间。
   * 如果只指定 globals 那么 locals 默认与提供的 globals 相同

2. 例子

.. code-block:: python

   # call.py
   x_call = 'x'
   def call():
       y_call = 'y'
       execfile('exc.py')

   call()


   # exc.py
   print 'x_call' in globals()
   print 'y_call' in globals()
   print 'x_call' in globals()
   print 'x_call' in locals()
   print x_call
   print y_call

   z_exc = 'z'
   print z_exc in globals()
   print z_exc in locals()
   def test():
       print z_exc

   test()

::

   results:
       True
       False
       False
       True
       x
       y
       False
       True
       Traceback (most recent call last):
         File "D:\workspace\test\call.py", line 6, in <module>
           call()
         File "D:\workspace\test\call.py", line 4, in call
           execfile('exc.py')
         File "exc.py", line 12, in <module>
           test()
         File "exc.py", line 10, in test
           print z_exc
       NameError: global name 'z_exc' is not defined

2. 稍作修改。

.. code-block:: python

   # call.py
   x_call = 'x'
   def call():
       y_call = 'y'
       execfile('exc.py')

   call()
   print z_exc


   # exc.py
   print 'x_call' in globals()
   print 'y_call' in globals()
   print 'x_call' in globals()
   print 'x_call' in locals()
   print x_call
   print y_call

   ######### here ##########
   global z_exc
   #########################
   z_exc = 'z'
   print z_exc in globals()
   print z_exc in locals()
   def test():
       print z_exc

   test()

::

   results:
       True
       False
       False
       True
       x
       y
       True
       False
       z
       z

3. 在 execfile 执行的module里新建的顶层变量 默认插入 locals() 里，不会添加到 globals() 里, execfile 执行的代码像是 另定义了一个函数
4. 如果不使用 调用函数 的名称空间的变量 那么 传入 {} 可以解决问题。

.. code-block:: python

   # call.py
   x_call = 'x'
   def call():
       y_call = 'y'
       execfile('exc.py',{})

   call()
   #print z_exc


   # exc.py
   print 'x_call' in globals()
   print 'y_call' in globals()
   print 'x_call' in globals()
   print 'x_call' in locals()
   #print x_call
   #print y_call

   z_exc = 'z'
   print z_exc in globals()
   print z_exc in locals()
   def test():
       print z_exc
   test()

::

   results:
       False
       False
       False
       False
       True
       True
       z

5. globals() 可以改变， locals() 不可以。

.. code-block:: python

   # call.py
   x_call = 'x'
   def call():
       y_call = 'y'
       execfile('exc.py',{})
       print x_call
       print y_call

   call()

   # exc.py
   #print 'x_call' in globals()
   #print 'y_call' in globals()
   #print 'x_call' in globals()
   #print 'x_call' in locals()

   globals()['x_call'] = 'x_changed'
   locals()['y_call'] = 'y_changed'

   #z_exc = 'z'
   #def test()：
   #    print z_exc

   #test()

::

   results:
        x_changed
        y

.. seealso::

   * 从编译角度理解全局变量与区部变量_
   * 局部名字空间与全局名字空间_

     .. _从编译角度理解全局变量与区部变量: http://hyry.dip.jp/tech/book/page/python/variable_scope_global.html
     .. _局部名字空间与全局名字空间: http://woodpecker.org.cn/diveintopython/html_processing/locals_and_globals.html

