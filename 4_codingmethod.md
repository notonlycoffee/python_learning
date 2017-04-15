
## 函数式编程(Functional Programming)

1.函数式编程就是一种抽象程度很高的*编程范式*，纯粹的函数式编程语言编写的函数**没有变量**，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为**没有副作用**。而允许使用变量的程序设计语言，由于函数内部的变量**状态不确定**，同样的输入，可能得到不同的输出，因此，这种函数是**有副作用的**。


2.函数式编程的一个特点就是，**允许把函数本身作为参数传入另一个函数，还允许返回一个函数**！


3.Python对函数式编程提供部分支持。由于Python允许使用变量，因此，**Python不是纯函数式编程语言**。


### 高阶函数(Higher-orderr function)

1.函数的名称是一个**变量**,可以是将函数直接指向一个变量,然后调用该变量进而可以调用该函数:

	>>> g = abs
	>>> g(-10)
	10


2.**函数名称也是一个变量**,当将函数名称这个变量指向其他的内容之后,就不能再调用之前的函数了:

	>>> abs = 10
	>>> abs(-10)
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	TypeError: 'int' object is not callable


**注意:**由于`abs`函数实际是定义在`import builtins`模块中的,所以修改`abs`变量指向其他内容的操作,要让这个操作在其他python模块中也生效,要用`import builtins; builtins.abs=10`


3.将函数作为一个参数,传入到另外一个函数中;那么一个函数可以接收另一个函数作为参数,则这样的函数就叫做**高阶函数**

比如:
	
	def add(x, y, f):
		return f(x) + f(y)

调用方式:

	x = -5
	y = 6
	f = abs
	va = add(-5, 6, abs)
	print(va)
	11




### map/reduce

#### map()

1.`map()`函数接收两个参数,一个是*函数*,一个是`Iterable`,`map`将传入的函数**依次作用**到序列的每个元素,并把结果作为心得`Iterator`返回

比如:

	def f(x):
		return x * x

	r = map(f, [1, 2, 3, 4, 5, 6])
	list(r)
	[1, 4, 9, 16, 25, 36]


由于返回的结果是一个`Iterator`,`Iterator`是惰性序列,因此通过`list()`函数把整个序列计算出来,返回一个list


上面的代码,使用循环也可以做出来,比如:
	L = []
	for n in [1, 2, 3, 4, 5, 6, 7, 8, 9]:
	    L.append(f(n))
	print(L)
但是使用上面的循环代码,不能直接看出来*把`f(x)`作用到list的每一个元素并把结果生成一个新的list*


2.map函数的应用举例:

*将所有数字转为字符串*
`>>> list(map(str, [1, 2, 3, 4]))`
['1', '2', '3', '4']



#### reduce()

1.`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上,这个函数必须接受**两个参数**,`reduce`把结果继续和序列的下一个元素做累积计算,其效果是:

`reduce(f, [x1, x2, x3]) = f(f(f(x1, x2), x3), x4)`

比方说,对一个序列求和,用`reduce`实现:

	from functools import reduce
	def add(x, y):
		return x + y

	reduce(add, [1, 3, 5, 7, 9])
	25

把序列变成一个整数:
	from functools import reduce
	def fn(x, y):
		return x * 10 + y

	reduce(fn ,[1, 3, 5, 7, 9])
	13579


把字符串转换成数值:
	from functools import reduce
	def fn(x, y):
		return x * 10 + y

	def char2num(s):
		return {'0':0, '1':1, '2':2, '3':3, '4':4, '5':5, '6':6, '7':7, '8':8, '9':9}[s]

	L = reduce(fn, map(char2num, '13579'))

	print(L)

	13579


字符串转换成数值进化版:
	from functools import reduce

	def str2int(s):
	    def fn(x, y):
	        return x * 10 + y
	    def char2num(s):
	    	return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[s]
	    return reduce(fn, map(char2num, s))


还可以使用`lambda`函数进一步简化:
	from functools import reduce

	def char2num(s):
	    return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[s]

	def str2int(s):
	    return reduce(lambda x, y: x * 10 + y, map(char2num, s))





**`reduce()`还可以接收第3个可选参数，作为计算的初始值**
比如以下代码:
	def fn(x, y):
		return x + y

	L = reduce(fn, [1, 2, 3, 4], 100)
	print(L)
	110

#### 插播global和nonlocal知识

1.`global`声明的变量,在python的整个模块中**只有一份**,使用`A`变量指向能够改变这个`global`变量的函数;同时使用`B`变量指向改变这个`global`变量的函数;当调用A改变了global变量,再调用`B`的时候,`global`变量已经被A改变;

`nonlocal`声明的变量,在`python`的整个模块中可以有**多份**,使用`A`变量指向能够改变这个`nonlocal`变量的函数;同时使用`B`变量指向改变这个`nonlocal`变量的函数;当调用`A`改变`nonlocal`变量,在调用`B`的时候,`B`中的`nonlocal`变量并没有因为`A`的调用而改变
	
下面给出示例代码:

**global变量**

	def tester(start):
		global state
		state = start
		def nested(label):
			global state
			print(label, state)
			state += 1
		return nested

	F = tester(0) ## F变量指向这个函数
	F('spam')

	spam 0

	F('eggs')

	eggs 1

	G = tester(42) ## G变量重新指向这个函数
	G('toast')

	toast 42

	G('bacon')

	bacon 43

	F('ham')  ##重新用F变量调用这个函数,数值却是G变量的
	ham 44


**这说明,不管函数被重新调用多少次,`global`关键字定义的变量,在整个模块中只有一份;**



**nonlocal变量**

	def tester(start):
		state = start
		def nested(label):
			nonlocal state
			print(label, state)
			state += 1
		return nested

	F = tester(0) ##变量F指向函数
	F('spam')

	spam 0

	F('ham')

	ham 1

	G = tester(42) ##变量G指向函数
	G('spam')

	spam 42

	G('eggs')

	eggs 43

	F('bacon') ##重新调用F变量的函数,nonlocal关键字的变量没有被G变量的函数覆盖

	bacon 3


**这说明,不管函数被重新调用多少次,`nonlocal`关键字定义的变量,在整个模块中可以有多份;**



#### 下面继续看reduce()实例:

1.将str转为float

	CHAR_TO_FLOAT = {
	    '0': 0,
	    '1': 1,
	    '2': 2,
	    '3': 3,
	    '4': 4,
	    '5': 5,
	    '6': 6,
	    '7': 7,
	    '8': 8,
	    '9': 9,
	    '.': -1
	}

	def str2float(s):
		nums = map(lambda ch: CHAR_TO_FLOAT[ch], s)
		point = 0
		def to_float(f, n):
			nonlocal point		##这里就使用了nonlocal关键字
			if n == -1
				point = 1
				return f
			if point == 0:
				return f * 10 + n
			else:
				point = point * 10
				retrun f + n / point
		return reduce(to_float, nums, 0.0)		##reduce可以接收第三个参数,作为初始化参数,下面会给出例子

	print(str2float('123.456'))

	123.456


2.`reduce`接收第三个参数作为初始化:

	def fn(x, y):
		return x + y

	L = reduce(fn, [1, 2, 3, 4, 5], 100)	##100作为初始值

	print(L)

	115



### filter()

1.`filter()`函数用于过滤序列.`filter()`接收一个函数和一个序列,返回一个`Iterator`**惰性序列**;
`filter()`把传入的函数一次作用于每个元素,然后根据返回值是`True`还是`False`决定保留还是丢弃该元素.

例如:删除偶数,保留奇数

	def is_odd(n):
		return n % 2 == 1

	list(filter(is_odd, [1, 2, 4, 5, 6, 9]))

	结果返回:[1, 5, 9]


把序列的空字符串去掉:
	
	def not_empty(s):
		return s and s.strip()	## strip方法移除头尾指定的字符,默认是空格


	list(filter(not_empty, ['A', '', 'B', None, 'C', '   ']))

	结果:['A', 'B', 'C']

函数`not_empty`返回的是`s and s.strip()`而不是返回`s.strip()`,那是因为,如果直接返回`s.strip()`,在遇到元素中包含有`None`的时候,就会报错;


2.使用filter求素数

用filter求素数

计算素数的一个方法是埃氏筛法，它的算法理解起来非常简单：

首先，列出从`2`开始的所有自然数，构造一个序列：

`2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...`

取序列的第一个数`2`，它一定是素数，然后用`2`把序列的`2`的**倍数**筛掉：

`3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...`

取新序列的第一个数`3`，它一定是素数，然后用`3`把序列的`3`的**倍数**筛掉：

`5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...`

取新序列的第一个数`5`，然后用`5`把序列的`5`的**倍数**筛掉：

`7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...`

不断筛下去，就可以得到所有的素数。

用Python来实现这个算法，可以先构造一个从`3`开始的奇数序列：

	def _odd_iter():
	    n = 1
	    while True:
	        n = n + 2
	        yield n
注意这是一个**生成器**，并且是一个**无限序列**。

然后定义一个筛选函数：

	def _not_divisible(n):
	    return lambda x: x % n > 0

最后，定义一个**生成器**，不断返回下一个素数：

	def primes():
	    yield 2
	    it = _odd_iter() # 初始序列
	    while True:
	        n = next(it) # 返回序列的第一个数
	        yield n
	        it = filter(_not_divisible(n), it) # 构造新序列

这个生成器先返回第一个素数`2`，然后，利用`filter()`不断产生筛选后的新的序列。

由于`primes()`也是一个无限序列，所以调用时需要设置一个退出循环的条件：

	#打印1000以内的素数:
	for n in primes():
	    if n < 1000:
	        print(n)
	    else:
	        break
注意到`Iterator`是惰性计算的序列，所以我们可以用Python表示“全体自然数”，“全体素数”这样的序列，而代码非常简洁。



### sorted
1.`sorted()`函数可以对list进行排序:

	sorted([36, 5, -12, 9, -21])
	[-21, -12, 5, 9, 36]


2.`sorted`可以接受一个key函数来实现自定义的排序,例如按绝对值大小排序:

	sorted([36, 5, -12, 9, -21], key=abs)
	[5, 9, -12, -21, 36]

key指定的函数将作用于list的每一个元素上，并根据**key函数返回的结果**进行排序。对比原始的list和经过`key=abs`处理过的list：

	list = [36, 5, -12, 9, -21]
	keys = [36, 5,  12, 9,  21]

然后`sorted()`函数按照keys进行排序，并按照对应关系返回list相应的元素：

	keys排序结果 => [5, 9,  12,  21, 36]
	                |  |    |    |   |
	最终结果     => [5, 9, -12, -21, 36]


3.对字符串进行排序:

	sorted(['bob', 'about', 'Zoo', 'Credit'])
	['Credit', 'Zoo', 'about', 'bob']

默认情况下，对字符串排序，是按照`ASCII`的大小比较的，由于`'Z' < 'a'`，结果，大写字母`Z`会排在小写字母`a`的前面。

可以把字符串变成小写或者大写之后再进行比较大小:

	sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
	['about', 'bob', 'Credit', 'Zoo']

要进行反向排序，不必改动key函数，可以传入第三个参数`reverse=True`：

	 sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
	['Zoo', 'Credit', 'bob', 'about']

4. `sorted()`函数中的`key`接收一个函数或者`lambda`函数

5.使用`itemgetter`根据维度排序:
`operator.itemgetter`函数,用于获取对象的哪些维的数据,参数是一些序号,比如:
	a = [1, 2, 3]
	b = operator.itemgetter(1) ## 先定义一个取第一个域的函数
	b(a)	##将这个函数作用在序列a上面
	2

`operator.itemgetter`声明的是一个函数,不会返回结果,需要将这个返回的函数**作用在一个对象中**,才能返回这个对象中的结果;

可以使用`operator.itemgetter获取域进行排序`:

	KK = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
	from operator iimport itemgetter
	def by_name(t):
		return itemgetter(0)(t)

	def by_score(t):
		return itemgger(1)(t)

	L1 = sorted(KK, key=by_name)
	L2 = sorted(KK, key=by_score)

	print(L1)
	print(L2)



### 返回函数

1.***高阶函数***除了可以接受函数作为参数外,还可以**把函数作为结果值返回**

比如普通的求和函数:

	def calc_sum(*args):
		ax = 0
		for n in  args:
			ax += n
		return ax

**如果直接返回函数:**

	def lazy_sum(*args):
		def sum():
			ax = 0
			for n in args:
				ax += n
			return ax
		return sum

调用方式:

	>>> f = lazy_sum(1, 3, 5, 7, 9)
	>>> f
	<function lazy_sum.<locals>.sum at 0x101c6ed90>
	>>> f()
	25

上面的例子中,我们在函数`lazy_sum`中定义了函数`sum`,并且**内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量,当`lazy_sum`返回函数`sum`时,相关参数和变量都保存在返回的函数中**,这种程序结构称之为*闭包(Closure)*


另外,当我们调用`lazy_sum()`,返回`sum`函数的时候,每次调用之后返回的`sum`函数都是不一样的:

	f1 = lazy_sum(1, 2, 3, 4, 5)
	f2 = lazy_sum(1, 2, 3, 4, 5)
	f1 == f2
	False


2.**闭包**

返回的函数在其定义内部引用了局部变量`args`,所以当一个函数返回了一个函数后,其内部的局部变量还被新函数引用;

返回的函数并没有立刻执行,而是直到调用`f()`才执行,比如下面的例子:

	def count():
		fs = []
		for i in rage(1, 4):
			def f():
				return i * i
			fs.append(f)
		return fs

	f1, f2, f3 = count()

该函数的运行结果会被错误的认为是:
`f1()`对应`1`; `f2()`对应`4`; `f3()`对应`9`;

但是实际的结果是:
`f1(), f2(), f3()`的结果都是`9`

原因在于函数引用了变量`i`, 但是它没有立刻执行,等到3个函数都返回时,它们所引用的`i`已经变成了`3`,因此最终结果为`9`


返回闭包时牢记的一点是:**返回函数不要引用任何循环变量,或者后续会发生变化的变量**

如果一定要使用循环变量怎么办?方法是再创建一个函数,用该函数的参数绑定循环变量当前的值,也就是将循环的值赋值给函数的参数,利用函数的参数进行写死这个循环变量,无论后续该循环变量如何更改,函数的参数都不会再继续变化:

	def count():
		def f(i):
			def g():
				return i * i
			return g
		fs = []
		for i in range(1, 4):
			fs.append(f(i))
		return fs

		>>> f1, f2, f3 = count()
		>>> f1()
		1
		>>> f2()
		4
		>>> f3()
		9




### 匿名函数

1.有些场合不需要显式地定义函数,直接传入匿名函数更加方便,匿名函数的语法是使用`lambda`进行表示:

使用`map()`函数进行说明:

	list(map(lambda x: x*x, [1, 2, 3, 4, 5]))
	[1, 4, 9, 16, 25]

上面的例子中,`lambda x: x * x`就是*匿名函数*的声明方式,对应的*定义函数*就是:
	def f(x):
		return x * x

关键字`lambda`表示匿名函数,冒号前面的`x`表示函数的参数

你I没那个函数有个**限制**:就是只能有一个表达式,不用写`return`,返回值就是该表达式的结果

用匿名函数有个好处，因为函数没有名字，不必担心*函数名冲突*。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数:

	f = lambda x : x * x
	f(5)
	25

也可以把匿名函数作为返回值返回:
def build(x, y):
	return lambda: x * x = y * y

**python对匿名函数的支持有限,只有一些简单的情况下可以使用匿名函数**



### 装饰器(decorator)

1.每个函数对象都有一个`__name__`属性,可以拿到函数的名字:

	def now():
		print('2017-04-04')

	>>> now.__name__
	'now'

	>>> f = now
	>>> f.__name__
	'now'


2.**装饰器**:

假设我们要增强`now()`函数的功能,比如在函数调用前后自动打印日志,但又不希望修改`now()`函数的定义,在**这种代码运行期间动态添加功能的方式,称之为"装饰器"**

代码如下:

	def log(func):
		def wrapper(*args, **kw):
			print('call %s()' % func.__name__)
			return func(*args, **kw)
		return wrapper

上面的`log`函数是一个**decorator**,**所以接受一个函数作为参数,并返回一个函数**,我们需要借助python的`@`语法,吧decorator置于函数的定义处:

	@log
	def now():
		print('2017-04=04')

现在调用`now()`函数,不仅会运行`now()`函数本身,还会运行`now()`函数前打印一行日志:

	>>> now()
		call now():
		2017-04-04

把`@log`放到`now()`函数定义处,相当于执行了语句:

`temp = log(now)`


由于`log()`函数是一个decorator,返回一个函数,所以原来`now()`函数仍然存在,知识现在`temp`变量指向了一个新的函数`wrapper`,于是调用`now()`将执行新函数

`wrapper()`函数里面用来执行需要的日志操作,然后再调用业务函数,实现decorator的功能


如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂.比如,要自定义log的文本:

	def log(text):
		def decorator(func):
			wrapper(*args, **kw):
				print('%s %s():' % (text, func.__name__))
				return func(*args, **kw)
			return wrapper
		return decorator

调用方式:

	@log('execute')
	def now():
		print('2017-04-04')

    >>> now()
    excute now():
    2017-04-04

调用的效果就是:`temp = log('execute')(now)`

以上的两种decorator的定义都没有问题,但是函数对象的`__name__`等属性从原来的`now`变成了`wrapper`

`>>> temp.__name__`
'wrapper'

因为返回的那个`wrapper()`函数名字就是`wrapper`,所以需要把原始函数`now`的`__name__`等属性复制到`wrapper()`函数中,否则,有些依赖函数签名的代码执行就会报错.复制的代码需要引入`functools`模块,代码如下:

	import functools
	def log(func):
		@functools.wraps(func)
		def wrapper(*args, **kw):
			print('call %s():' % func.__name__)
			return func(*args, **kw)
		return wrapper


或者针对带参数的decorator:

	import functools
	def log(text):
		def decorator(func):
			@functools.wraps(func)
			def wrapper(*args, **kw):
				print('%s %s():' % (text, func__name__))
				return func(*args, **kw)
			return wrapper
		return ddecorator




### 偏函数

1.偏函数可以让函数的调用更加简单,总结起来说就是,偏函数可以绑定部分的参数,让传入的参数减少,实现简化的函数调用,比如下面的场景:
`int()`函数有两个参数,一个是需要转换的值,另一个是转换的基数`base`,默认是`10`,也就是把目标转换成**十进制**;
我们可以很方便的将目标参数转化为十进制,只要单纯的使用`int()`函数就可以了,但是如果我们需要转换大量的二进制函数,就需要明确地调用
`int('123', 2)`,或者我们需要自己编写一个可以转换二进制的方法:

	def int2(num, base=2):
		return int(num, base)

但是,我们完全可以使用偏函数完成我们的需求,添加一个默认是转成二进制的函数:

	import functools
	int2 = functools.partial(int, base=2)
	int2('1000000')
	64

`int2`现在就是只需要传入一个目标参数的函数,默认转为二进制
所以，简单总结`functools.partial`的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。

虽然`int2`默认转换成二进制,但是还是可以用来转换成十进制,只不过需要明确地指定`base`参数:

`int2('10000', 10)`

`int2 = functools.partial(int, base=2)`

实际上固定了`int()`函数的关键字参数`base`，也就是：

`int2('10010')`

相当于：

	kw = { 'base': 2 }
	int('10010', **kw)


当传入：

`max2 = functools.partial(max, 10)`

实际上会把`10`作为`*args`的一部分自动加到左边，也就是：

`max2(5, 6, 7)`

相当于：

	args = (10, 5, 6, 7)
	max(*args)

结果为`10`。


当函数的参数个数太多，需要简化时，使用`functools.partial`可以创建一个新的函数，这个新函数可以**固定**住原函数的部分参数，从而在调用时更简单。