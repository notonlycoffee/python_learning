
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

将str转为float

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
			nonlocal point
			if n == -1
				point = 1
				return f
			if point == 0:
				return f * 10 + n
			else:
				point = point * 10
				retrun f + n / point
		return reduce(to_float, nums, 0.0)

	print(str2float('123.456'))
	
	123.456




