## 基础知识点
1. 基本数据结构list（mutable）、tuple（immutable）、dict(keys, values)、set(不重复)...
2. 内置函数： range(start, end), max(…), len(...)
3. 可迭代对象 dict 、list（append, pop）
4. 切片[start:end]包含start，不含end,可用于str、list、tuple；列表生成式
5. 生成器generator，迭代器Iterator
6. 函数式编程 map、reduce、filter、sorted，高阶函数、柯理化（lazy） 、闭包
7. lambda匿名函数，装饰器、functools偏函数
8. 模块、作用域
9. 对象：类、实例，继承、多态
10. 获取对象信息：type、isinstance、dir()、help()获得一个对象的所有属性和方法
11. 处理try...except...finally...、断言assert，日志logging，打印 print
12. 复制与深复制：copy模块的copy方法以及deepcopy
13. 单元测试，python自带unittest模块
14. io编程：同步、异步，open、read、write等方法，StringIO、BytesIO模块
15. 操作文件和目录：os模块，os.name / os.uname / os.environ / os.path / os.rename / os.remove…
16. 序列化：pickle模块，pickle.dump把任意对象序列化成一个bytes，pickle.loads()方法反序列化出对象；json模块；Google 的protobuf
17. 多任务的实现：多进程模式、多线程模式、多进程+多线程模式
18. 正则：re模块，贪婪匹配、分组
19. 常用的内建模块：datetime、collections、base64、struct、hashlib、hmac、itertools、contextlib、urllib、XML、HTMLParser
20. 常用第三方模块：Pillow、requests、chardet、psutil、virtualenv
21. 网络编程：socket
22. WSGI：Web Server Gateway Interface。
23. 访问数据库：mysql-connector-python驱动连接到MySQL服务器
24. ORM：SQLAlchemy，Object-Relational Mapping，把关系数据库的表结构映射到python对象上
25. 异步io： generator、asyncio、async、await
26. 垃圾回收：使用的主要算法是引用计数.实际上,每个对象都会统计有多少引用指向自己.当引用计数归零时,对象立即就被销毁
27. 代码风格指南：pep8
28. debugger：pdb模块，logger：logging模块
29. 单行注释#， 多行注释三个单引号或三个双引号


## 重难点
####    理解闭包
1. 首先作用域是一层一层嵌套的，内部的可以访问外部作用域，外部不能 访问内部作用域
2. 内部函数对外部被包含函数变量的引用
3. 作用：代码封装、复用
4. 柯理化，以函数作为返回值

	```
	def set_passline(passline):
  		def is_pass(val):
	   		if val > passlne:
	       	print(’succes’)
	       print(‘failed')
		return is_pass
	```

5. 高阶函数：以函数作为参数

	```
	def dec(func):
	    def in_dec(*arg):
		  	if len(arg) == 0:
		      	return 0
		  	for val in arg:
		    	if not isinstance(val, int):
		        	return 0
		   	return func(*arg)
  		return in_dec
	
	def my_sum(*arg):
		return sum(arg)

	my_sum = dec(my_sum)
	```
            
6. 装饰器

	```
    @dec
	 def my_sum(*arg):
       return sum(arg)
   ```
        
#### 迭代器与生成器
1. 迭代器有两个基本的方法：iter() 和 next()。

	```
	list=[1,2,3,4]
	it = iter(list)    # 创建迭代器对象
	print (next(it))   # 输出迭代器的下一个元素
	>>> 1
	```
2. 使用了 yield 的函数被称为生成器（generator），成器是一个返回迭代器的函数，只能用于迭代操作，更简单点理解生成器就是一个迭代器。在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，返回 yield 的值, 并在下一次执行 next() 方法时从当前位置继续运行；

	```
	import sys
	def fibonacci(n): # 生成器函数 - 斐波那契
	    a, b, counter = 0, 1, 0
	    while True:
	        if (counter > n): 
	            return
	        yield a
	        a, b = b, a + b
	        counter += 1
	f = fibonacci(10) # f 是一个迭代器，由生成器返回生成
	 
	while True:
	    try:
	        print (next(f), end=" ")
	    except StopIteration:
	        sys.exit()

	```

#### 异步IO
1. 同步io会阻塞代码的执行

	```
	do_some_code()
	f = open('/path/to/file', 'r')
	r = f.read() # <== 线程停在此处等待IO操作结果
	# IO操作完成后线程才能继续执行:
	do_some_code(r)

	```
2. 异步模型，异步io发出io指令，它并不阻塞代码的执行，而是等到io返回结果时再通知CPU处理

	```
	# 需要一个消息队列，不断的从中读取消息并处理消息
	loop = get_event_loop()
		while True:
		    event = loop.get_event()
		    process_event(event)
	```
3. 协程，使用协程实现生产者-消费者模式

	```
	def consumer():
	    r = ''
	    while True:
	        n = yield r
	        if not n:
	            return
	        print('[CONSUMER] Consuming %s...' % n)
	        r = '200 OK'
	
	def produce(c):
	    c.send(None)
	    n = 0
	    while n < 5:
	        n = n + 1
	        print('[PRODUCER] Producing %s...' % n)
	        r = c.send(n)
	        print('[PRODUCER] Consumer return: %s' % r)
	    c.close()
	
	c = consumer()
	produce(c)
	```
	
	执行结果：

	```
	[PRODUCER] Producing 1...
	[CONSUMER] Consuming 1...
	[PRODUCER] Consumer return: 200 OK
	[PRODUCER] Producing 2...
	[CONSUMER] Consuming 2...
	[PRODUCER] Consumer return: 200 OK
	[PRODUCER] Producing 3...
	[CONSUMER] Consuming 3...
	[PRODUCER] Consumer return: 200 OK
	[PRODUCER] Producing 4...
	[CONSUMER] Consuming 4...
	[PRODUCER] Consumer return: 200 OK
	[PRODUCER] Producing 5...
	[CONSUMER] Consuming 5...
	[PRODUCER] Consumer return: 200 OK
	```
	注意到`consumer `函数是一个`generator `，把一个`consumer `传入`produce`后：

	首先调用`c.send(None)`启动生成器；

	然后，一旦生产了东西，通过`c.send(n)`切换到`consumer `执行；

	`consumer `通过`yield `拿到消息，处理，又通过`yield `把结果传回；

	`produce `拿到`consumer `处理的结果，继续生产下一条消息；

	`produce `决定不生产了，通过`c.close()`关闭`consumer`，整个过程结束。

