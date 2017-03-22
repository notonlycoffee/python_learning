## 高级特性:

python的代码原则:**代码越少,开发效率越高**


### 切片(slice)

1.抛出问题:
对于一个list集合,我们要取出前3个元素,一般的想法是使用循环进行,但是python可以使用更加简洁的方式:


`>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']`

`>>> L[0:3]`

结果显示:`['Michael', 'Sarah', 'Tracy']`

`L[0:3]`表示，从索引`0`开始取，直到索引`3`为止，但不包括索引`3`。即索引`0，1，2，`正好是`3`个元素。

`>>> L[:3]` 也可以达到一样的效果,默认从`0`开始索引

`>>> L[1:3]`  取出1,2两个元素

类似的，既然Python支持`L[-1]`取倒数第一个元素，那么它同样支持倒数切片

`>>> L[-2:]`

结果显示:`['Bob', 'Jack']` 

倒数第一个的元素所以是**`-1`**


2.切片操作2

我们先创建一个0-99的有序序列:

`>>> L = list(range(99))`

取出前10个数:	`>>> L[:10]`

取出后10个数:	`>>> L[-10:]`

取前11-20:		`>>> L[10:20]`

前10个数,每两个取出来一个:	`>>> L[:10:2]`

结果显示:`[0, 2, 4, 6, 8]`

所有数,每5个取一个:		`>>> [::5]`

结果显示:`[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]`

甚至什么都不写，只写[:]就可以原样复制一个list：

`>>> L[:]`

结果显示: `[0, 1, 2, 3, ..., 99]`


3.其他数据类型的切片

**`tuple`切片**

`>>> L = (0, 1, 2, 3, 4, 5)`

`>>> L[:3]`

结果显示:`(0, 1, 2)`


**`str`切片(字符串切片)**

字符串`'xxx'`也可以看成是一种list，每个元素就是一个字符。

`>>> 'ABCDEFG'[:3]`

结果显示:`'ABC'`

`>>> 'ABCDEFG'[::2]`

结果显示:`'ACEG'`

在很多编程语言中，针对字符串提供了很多各种截取函数（例如，substring），其实目的就是对字符串切片。Python没有针对字符串的截取函数，只需要切片一个操作就可以完成，非常简单


### 迭代

1`.迭代的语法:`for....in`

比如:

	L = [1, 2, 3, 4, 5]
	for n in L:
		print(n)

	1
	2
	3
	4
	5



2.迭代dict的key-value方式:

	d = {'a':1, 'b':2, 'c':3}

	#只打印key值
	for key in d:
		print(key)

	#只打印value值
	for value in d.values():
		print(value)

	#同时打印key和value
	for k, v in d.items():
		print("%s = %s" %(k,v))


3.判断一个对象是否可以使用迭代:
方法是通过`collections`模块的`Iterable`类型判断 

`>>> from collections import Iterable`

`>>> isinstance('abc', Iterable)`
True

`>>> isinstance([1, 2, 3], Iterable)`
True

`>>> isinstance(123, Iterable)`
False

4.如果需要对list集合实现类似java那样的下标循环,可以使用python内置的`enumerate`函数,把一个list变成***索引-元素***对:

	for i, value in enumerate(['A', 'B', 'C']):
	...	print(i,value)
	...


	0	A
	1	B
	2	C





### 列表生成式(List Comprehensions)

*是python内置的非常强大的可以用来创建list的生成式;*

1.普通操作:
生成1-10的列表:

`>>> list(range(1, 11))`

显示的结果是:`[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`

如果要生成`[1x1, 2x2, 3x3, ..., 10x10]`,就有更加简便的方式:

`>>> [x * x for x in range(1, 11)]`

结果显示:`[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]`

2.
`for`循环后面还可以带上`if`判断:

`>>> [x * x for x in range(1, 11) if x % 2 == 0]`

显示结果`[4, 16, 36, 64, 100]`

还可以使用多重循环,这里用双重循环举例说明:

`>>> [m + n for m in 'ABC' for n in 'XYZ']`

结果显示`['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']`


可以利用列表生成式列出目录下面的文件和目录名:

`>>> import os`
`>>> [d for d in os.listdir('.')]`

结果会显式当前目录下面的所有文件和目录名


3.列表生成式可以使用多个变量:

`>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }`

`>>> [k + '=' + v for k, v in d.items()]`

结果显示:`['y=B', 'x=A', 'z=C']`


可以在列表生成式生成list的时候,对生成的元素进行一些操作,比如将字符串转换成小写的:

`>>> L = ['Hello', 'World', 'IBM', 'Apple']`

`>>> [s.lower() for s in L]`

结果显示:`['hello', 'world', 'ibm', 'apple']`


### 生成器(generator)

什么是生成器

通过列表生成式，我们可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。

所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。

1.创建生成器的方式:

**方式1:**

`>>> L = [ x * x for x in range(10)]`

`>>> L`

结果显示:`[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]`

`>>> g = (x * x for x in range(10))`

`>>> g`

结果显示:`<generator object <genexpr> at 0x1022ef630>`

创建`L`和`g`的区别仅在于最外层的`[]`和`()`，`L`是一个`list`，而g是一个`generator`。

可以通过调用方法`next()`函数获得生成器的下一个返回值:

	>>> next(g)
	0
	>>> next(g)
	1
	>>> next(g)
	4
	...
	...
	...
	>>> next(g)
	81
	>>> next(g)
	Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
	StopIteration


`generator`保存的是**算法**，每次调用`next(g)`，就计算出`g`的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出`StopIteration`的错误。


同时也可以使用循环获取下一个值(这种方式不会抛出`StopIteration`的错误)


	g = (x * x for x in range(10))
	for n in g:
		print(n)

	0
	1
	4
	...
	...
	81



**方式2:**

generator非常强大。如果推算的算法比较复杂，用类似列表生成式的for循环无法实现的时候，还可以用函数来实现。

举例:*斐波那契数列*

`1, 1, 2, 3, 5, 8, 13, 21, 34, ...`

使用函数写出来就是:

	def fib(max):
		n, a, b = 0, 0, 1
		while n < max:
			print(b)
			a, b = b, a + b
			n = n + 1
		return 'done'

调用方式:

	>>> fib(6)
	1
	1
	2
	3
	5
	8
	'done'


可以看出，`fib`函数实际上是定义了斐波拉契数列的推算规则，可以从第一个元素开始，推算出后续任意的元素，这种逻辑其实非常类似`generator`。

也就是说，上面的函数和`generator`仅一步之遥。要把`fib`函数变成`generator`，只需要把**`print(b)`**改为**`yield b`**就可以了,下面看代码:

	def fib(max):
		n, a, b = 0, 0, 1
		while n < max:
			yield b
			a, b = b, a + b
			n = n + 1
		return 'done'
	




