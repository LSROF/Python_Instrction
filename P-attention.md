# Python 启动入口

```python
if __name__ == '__main__':
```



# 面对过程思维

> 按程序执行的流程,进行编写,重点关注的是程序执行的顺序.



# 面对对象思维

> 创建可以完成任务的对象，之后操控对象.



# 下载源（清华源）

> https://mirrors.tuna.tsinghua.edu.cn/help/pypi/
>
> 使用：pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple



# 同步异步

> **同步**是指完成事务的逻辑，先执行第一个事务，如果阻塞了，会一直等待，直到这个事务完成，再执行第二个事务，顺序执行.
>
> **异步**是和同步相对的，异步是指在处理调用这个事务的之后，不会等待这个事务的处理结果，直接处理第二个事务去了，通过状态、通知、回调来通知调用者处理结果



# 类与对象

> 变量 = 类名字()	# 创建对象(实例化).
>
> 变量.方法()	# 执行类中定义的方法.	**self不用传参**.
>
> 对象可执行功能在类中体现.

```python
class 类名字:
    '''
    类中函数，如果有参数self，就可以称之为方法.
    '''
    def 方法(self):
        方法体
        pass
```



# ^ self

> 方法名称中前后含__，会在特定的场景下自动运行.
>
> **self表示的是当前正在初始化的对象**

```python
class 类:
    
    def __init__(self, param):	# 初始化该对象，在创建对象时，自动执行方法体.
        self.param = param
        
```



# 面对对象的内存逻辑

> 每一个对象都是一个独立的内存.
>
> 该对象执行方法时，自动把该对象（包括内存地址）传递过去，**接受该对象的参数是self.**
>
> 如果对象和类中方法相同，则执行对象本身的方法.



# ^ 类成员



## -类变量（有些人称之为静态属性）

> 所有的当前这个类的对象，共享这个变量.
>

```python
class kind:
    variable = parameter	# 类变量
    def __init__(self):
        pass
```



## -方法（实例方法）

> 类中正常的使用的方法.



## -类方法

> **该方法第一个参数是（cls）** 
>
> 在方法上写出 @classmethod，这个方法就是一个类方法.
>
> @classmethod	# 使用场景大多数，创建对象-并初始化.
>
> 类方法一般不用对象来调用，一般用类来调用.

```python
class Demo:
   	
    def __init__(self, number):
        self.number = number
    
    def delete(self):
    	return f"已删除：{self.number}"
    
    @classmethod	# 类方法
    def default(cls):
        result = cls(1)
        return result


finish = Demo.default() # 使用类进行调用
print(finish.delete())
```



## -静态方法

> 在方法上面添加	@staticmethod.
>
> **本质上是声明在类中的函数.**
>
> 参数上和正常函数一样，无需	cls	self.
>
> 使用场景：创建和初始化一个对象

```python
class calculate:
    
    @staticmethod
    def count(x, y):	# 类中的函数
        return x + y
    
 
print(calcuate.count(0, 1))
```



## -属性方法

> 使用：在方法上添加	@property.	(此时该方法转变为属性方法)
>
> 使用场景：某一个属性，必须经过计算才能得到，我们需要把方法修改为一个属性.
>
> **注意：此时的方法名应该改为属性名.**

```python
class count:
    
    def __init__(self, year):
        self.year = year
    
    @property	# 取值
    def age(self):	# 参数只需要self. (属性名)
        return 2023 - self.year
  	
    @age.setter	# 向某个属性，设置值
	def age(self, number):
       	self.year = 2023 - number

result = count(2005)
print(result.age)	# 对象.属性名
result.age = 23
print(result.year)
```



## -特殊方法

> python在每一个类中都内置了一大堆的方法，这些方法在特殊场景下被自动调用.
>
> 大多数的特殊方法和python的内置函数相关.

```python
__len__() => len()
__iter__() => iter()

与计算相关
__add__() => 对象 + x

和属性相关
__getitem__() => 对象["x"]
__setitem__() => 对象["x"] = 值

初始化相关
__new__() => 创建对象时分配内存
__init__() => 初始化对象时自动调用

和上下文with相关
__enter__()	进入with的时候
__exit__()	离开with的时候
```

```python
class demo:

    def __abs__(self):
        return print("绝对值")

    def __len__(self):
        return print("长度")

    def __add__(self, other):
        return print("添加" + other)

    def __iter__(self):
        return print("迭代器")

    def __getitem__(self, item):
        return print("创建字典")

    def __setitem__(self, key, value):
        return print("更改字典")

    def __del__(self):  # 程序执行完成会自动执行一次.
        return print("完成删除")

    def __enter__(self):
        return print("进入with")

    def __exit__(self, exc_type, exc_val, exc_tb):
        return print("退出with")

    # 创建对象的过程：__new__() => __init__()
    def __new__(cls, *args, **kwargs):  # 创建对象的地方.
        print("创建对象")
        return object.__new__(cls)  # 分配内存，构建出一个对象.

    def __init__(self):
        print("init")


result = demo()
result.__abs__()
result.__len__()
result.__add__("成功")
result.__iter__()
result.__getitem__("one")
result["one"] = 1
# del result
with result as x:
    pass
```



# 字节与字符串的相互转换

```python
x = b'Hello'
y = "Hello"
print(str(x, encoding="utf-8"))
print(bytes(y, encoding="utf-8"))
```



# 小数位数量

```python
number = 1.0
print("{:.3f}".format(number))
```



# ^ 继承



## -继承

> 继承：子类会自动拥有父类中除了私有内容外的其他所有内容.
>
> ​		   本身拥有的方法不会调用.
>
> 类中以双下划线开头的方法是私有方法.

```python
class animal:

    def run(self):
        return "run"
	
    def __eat(self):	# 所有以双下划线开头的方法都是私有方法.
    	return "eat"
        

class dog(animal):	# 继承animal中除了已有方法的方法
    pass


class cat(animal):
    pass


class fish(animal):

    def run(self):
        return "swim"


print(dog().run())
print(cat().run())
print(fish().run())
```



## -重写 覆盖

> 子类可以对父类的功能或者方法进行重新定义.



## -子类中运行父类被重写的方法

> super(子类(或者是父类的前一个父类)，self).方法()	
>
> super().方法()	# 通过super()来调用父类的方法.



## -多继承

> 优秀按顺序寻找方法继承

```python
class number:

    def one(self):
        return "n_one"


class digit:

    def one(self):
        return "d_one"

    def two(self):
        return "d_two"


class person(number, digit):

    def result(self):
        print(self.one())
        print(self.two())

    def one(self):
        print(super(number, self).one())
        return "p_one"


P = person()
P.result()
```



# 继承路径

> **查看类的所有继承关系(继承路径)：类名.mro()**
>
> python 中所有的类都会默认继承Object.
>
> Object 为所有类的根.



# 模板模式 - 框架

> Django

```python
class Frame:

    def start(self):
        self.one()
        self.two()
        self.three()

    def one(self):
        pass

    def two(self):
        pass

    def three(self):
        pass


class Number(Frame):

    def one(self):
        return print("1")

    def two(self):
        return print("2")

    def three(self):
        return print("3")


N = Number()
N.start()
```



# 约束

> **使用场景：框架**
>
> 在父类的方法中写入以下代码，代表约束子类必须重写该方法，否则报错.
>
> **raise NotImplementedError("报错提示")** 
>
> **raise** ：表示抛出一个异常.	(主动报错)



# List内置for循环

> **list = [ 表达式 for 循环项 in 循环体 (if 条件) ]**

```python
ls = dir(class_name)
ls = [func for func in ls if not func.startswich("__")]
print(ls)
```



# ^ 反射

> **hasattr(obj，name)**	# 从obj对象中判断是否存在name属性或功能.
>
> **getattr(obj，name)**	# 从obj对象中获取到name属性或功能.
>
> **setattr(obj，name，value)**	# 动态给obj对象设置某个属性或方法,值是value.
>
> **delattr(obj，name)**	# 从obj对象中删除某个属性，慎用 . . .

```python
# exercise.py

class tools:

    number = 1

    def delete(self):
        return "删除"

    def update(self):
        return "修改"

    def append(self):
        return "添加"

    def search(self):
        return "查找"
```

```python
# demo.py

import exercise
from exercise import tools


ls = dir(tools)
ls = [func for func in ls if not func.startswith("__")]

if __name__ == "__main__":
    print("输入 q 或 Q 退出程序.")
    while 1:
        print("方法名：", ls)
        enter = input("输入需要测试的方法名：").lower().strip()
        decide = hasattr(exercise.tools(), enter)

        if enter.upper() == "Q":
            break

        if decide:
            run = getattr(exercise.tools(), enter)
            if callable(run):
                print(run())
            else:
                print("该属性的值为:", run)
        else:
            print("没有此方法，请重新输入.")
```



# ^ 多线程和多进程

> **多线程：from threading import Thread** 
>
> **多进程：from multiprocessing import Process** 



## -第一种创建多线程方法 直接创建线程

> **T = threading.Thread(target=work, args=("server_one", ))**	# 创建线程对象
>
> target：让线程执行什么任务.
>
> args：线程启动之后，target执行时需要传递的参数.	# **参数类型为 tuple (元组).**
>
> T.start()
>
> T.join()

```python
import threading
import time
import random
from threading import Thread


def work(server):

    begin = time.time()

    print("检查", server)
    use = random.randint(1, 3)
    time.sleep(use)
    print("关闭", server)

    end = time.time()

    print("本次用时大约：{:.5f}秒".format(end - begin))


if __name__ == "__main__":	# 主线程
    t1 = Thread(target=work, args=("server_one", ))		# 子线程
    t2 = Thread(target=work, args=("server_two", ))		# 子线程
    t3 = Thread(target=work, args=("server_three", ))
    t1.start()	# 告诉CPU，该线程可以启动.
    t2.start()
    t3.start()
```



## -概念

> **进程是资源分配的最小单位，线程是CPU调度的最小单位**
>
> 线程：一个进程会有多个线程，至少拥有一个线程.
>
> 进程：正在进行中的程序.	# 资源单位，不是执行单位.
>
> 线程：员工
>
> 进程：公司
>
> 每次运行程序都会至少产生一个单独的进程，该进程中会有一个主线程存在.



## -第二种创建线程方法 继承Thread类

> **并在自己的类中，重写run方法.** 

```python
import time
import random

from threading import Thread  # 线程


# 继承Thread类，并在自己的类中，重写run方法.
class MyThread(Thread):

    def __init__(self, server):
        self.server = server
        super(MyThread, self).__init__()    # 子类如果重写父类中的__init__()， 必须调用父类的__init__().

    # run()：线程启动之后需要执行的任务.
    def run(self) -> None:  # 表示返回值类型应该是None.	需使用__init__()进行传参.
        print("开始测试：", self.server)
        time.sleep(random.randint(1, 5))
        print("结束测试：", self.server)


# if __name__ == "__main__":
#     b1 = MyThread("server_one")
#     b2 = MyThread("server_two")
#     b3 = MyThread("server_three")
#     # 启动线程使用start().
#     b1.start()
#     b2.start()
#     b3.start()
    
    
    
if __name__ == "__main__":
    for count in range(1, 101):
        variable = "server" + str(count)
        begin = MyThread(variable)
        begin.start()
```



## -join()

> 需要等待子线程结束后，才会继续执行主线程.
>
> 等待过程中，其他线程也在运行.

```python
if __name__ == "__main__":
    b1 = MyThread("server_one")
    b2 = MyThread("server_two")
    
    # 启动线程使用start().
    b1.start()
    b2.start()

    b1.join()
    b2.join()

    print("service")
```



## -注意

> 不要让多线程变成单线程了

```python
if __name__ == "__main__":
    Thread_list = []
    for count in range(1, 101):
        variable = "server" + str(count)
        begin = MyThread(variable)
        Thread_list.append(begin)

    print(Thread_list)

    for server_begin in Thread_list:
        server_begin.start()

    for server_end in Thread_list[::-1]:
        server_end.join()
        
    print("service")
```



## -创建多进程

> 创建使用方法类似多线程

### -- 第一种（最常用）

```python
from multiprocessing import Process


def work():

    for number in range(10000):
        print("子进程", number)


if __name__ == "__main__":

    p = Process(target=work)
    p.start()

    for n in range(10000):
        print("主进程", n)
```



### -- 第二种（几乎不用）

```python
from multiprocessing import Process


class MyProcess(Process):

    def run(self) -> None:
        for digit in range(10000):
            print("子进程", digit)


if __name__ == '__main__':

    p = MyProcess()
    p.start()

    for number in range(10000):
        print("主进程", number)

```



# ^ 何时用多线程，何时用多进程

> **多线程：多个任务的逻辑完全一样.**
>
> **多进程：任务之间要隔离，没有什么相似性.**	**# 很少有关联**
>
> 可参考	生产消费模型

```python
# 伪代码：生产消费模型

def 生产():
    for i in range(10):
        t = Thread(target=下载)
        t.start()
        
        
def 消费():
    for i in range(3):
        t = Thread(target=检测)
        t.start()
        
        
p1 = Process(target=生产)
p2 = Process(target=消费)

p1.start
p2.start
```



# ^ 线程池

> **from concurrent.futures import ThreadPoolExecutor**
>
> **with ThreadPoolExecutor(线程数) as f:** 
>
> ​	**t.submit(任务)**  

```python
import time

from random import randint
from concurrent.futures import ThreadPoolExecutor


def work(server):
    print("start：", server)
    time.sleep(randint(1, 3))
    print("finish：", server)


if __name__ == '__main__':
    server_list = [f"server{number}" for number in range(1, 101)]
    with ThreadPoolExecutor(10) as t:   # 创建一个拥有10个线程的线程池.
        for found in server_list:
            t.submit(work, (found, ))   # Thread(target=work, args=(found, ))
```

```python
t = ThreadPoolExecutor(10)
for found in server_list:
	t.submit(work, (found, ))
t.shutdown()	# 如果需要手动去join,需要使用shutdown()进行等待.
```



# Requests模块 （第三方模块）

> resp = requests.get(url, timeout=1)	发送请求出去，超时时间是1秒钟.
>
> resp.test => 获取响应文本
>
> resp.json() => 获取响应数据，并自动转化成字典，前提是保证返回的数据是json格式.

> **帮我们完成http请求	而不是tcp请求.**
>
> **安装：在Terminal中输入 pip install 模块** 	# 默认下载源为：pipy.org
>
> 可以在pychram中setting中的python解释器中安装

```python
import requests     # 模拟浏览器的请求过程

# 检测：通过访问该服务器上的某个URL是否可以获取到返回内容.

url = "http://127.0.0.1:1000/test"

# 发送get请求   和在网址栏中enter了差不多
send = requests.get(url, timeout=1)	# 超过1秒钟报错

'''
dic = send.json()	# 前提是保证返回的数据是json,直接将server返回的内容处理成为字典.
'''

print(send.text)    # 打印响应的内容
```



# ^ 协程	(提升CPU的利用率)

> 在单线程逻辑下，当某个任务进入阻塞状态(CPU没有办法帮你工作的时候)时，自动切换到其他任务上。	# 该逻辑称之为协程(规避阻塞).

> 如果你的程序遇到了非常耗时的非计算类的操作的时候，你的CPU时不为你工作的.
>
> 非常耗时的非计算类的操作： 
>
>  	1.	文件读写
>  	2.	网络数据传输
>
> 上述操作称之为IO操作.        # IO = INPUT + OUTPUT

> 事件循环：观察每一个被挂起的任务状态。如果阻塞，就保持挂起，如果不阻塞，就进入就绪状态，可以被执行.
>
> 挂起：当程序遇到了阻塞，遇到的IO，需要我们手动把任务挂起.

> 在python3.6之后，官方给出了asyncio模块，提供了async和await关键字.

```python
import asyncio


# 定义了一个协程函数
async def func():
    print("Hello")
    # return "World"


if __name__ == '__main__':
    f = func()  # 此时并不是把函数给运行，而是创建了协程对象.
    # 如何运行协程对象/函数
    # 1.使用asyncio.run(协程函数)
    # asyncio.run(f)  # 有可能有问题(windows)	event loop has closed 时，使用另一种方法.
    # 2.用事件循环
    # event_loop = asyncio.get_event_loop()   # 3.10版本之前使用
    event_loop = asyncio.new_event_loop()     # 3.10版本之后使用
    event_loop.run_until_complete(f)
    # 3.在async函数内
    # await func()
```

```python
import asyncio


# 多任务异步协程
async def send(url):
    # 模拟发送请求的等待: asyncio.sleep(3)   不能使用time.sleep()
    print("请求发送:", url)
    # await 挂起. 自动切换到其他任务，语法上:要求await必须在async函数内.
    await asyncio.sleep(3)
    # 当上面的阻塞被解除了，就能继续运行.
    print("run complete is", url)


async def main():
    # 多任务
    ls = [f"ls{number}" for number in range(1, 6)]
    tasks = []
    for demo in ls:
        # 把协程对象包装成任务
        task = asyncio.create_task(send(demo))
        tasks.append(task)
    await asyncio.wait(tasks)   # 集体等待所有任务执行完毕
    print("program run complete")

    
if __name__ == '__main__':
    # 单任务
    # asyncio.run(send("https://github.com"))
    asyncio.run(main())
```



## -await

> **await 后面跟协程函数	与普通的函数调用没有什么区别** 

```python
import asyncio


async def func1():
    print("hello_one")
    await asyncio.sleep(1)
    print("complete_one")
    return "return"


async def func2():
    print("hello_two")
    ren = await func1()
    print("complete_two", ren)


async def func3():
    print("hello_three")
    await func2()
    print("complete_three")


if __name__ == '__main__':
    asyncio.run(func3())
```



## -aiohttp

> 实现访问网站

```python
import asyncio
import aiohttp


async def send(url):    # 单个任务
    # 默认的等待时间太长，超过1秒没有反应，就应该有问题了.
    # 设置超时时间：最大等待时间
    time = aiohttp.ClientTimeout(1)
    # 开始发送请求
    # 1.创建session对象
    # async：异步
    # async with: 开始的时候运行__aenter__ 离开的时候自动运行__aexit__
    try:
        async with aiohttp.ClientSession(timeout=time) as session:
            # 2.可以发送请求
            async with session.get(url) as resp:
                # 3.从响应对象中获取到结果
                result = await resp.text(encoding="utf-8")
                # print(result)
                print("正常", url)
    except UnicodeDecodeError:
        print("解码错误：", url)
    except TimeoutError:
        print("超时错误：", url)


async def main():
    ls = [f"https://www.{i}.com" for i in ["baidu", "qq", "google"]]
    # ls = ["http://www.baidu.com", "http://www.qq.com", "http://www.google.com"]
    tasks = []
    for _url in ls:  # 拿到每一个url
        task = asyncio.create_task(send(_url))  # 每一个url构建一个任务
        tasks.append(task)  # 收集所有任务对象
    await asyncio.wait(tasks)   # 统一运行所有任务，等待所有任务执行完成.


if __name__ == '__main__':
    asyncio.run(main())
```



# 网络编程

> ip + port => 网络中某台机器的某个应用程序

## 	- TCP编程 

> 特点：连续的，可靠的，效率较低.

### -- 服务器

```python
sk = socket.socket()
sk.bind((ip, port))
sk.listen()
conn, address = sk.accept()

conn.send()
conn.recv()

```



### -- 客户端

```python
sk = socket.socket()
sk.connect((ip, port))

sk.send()
sk.recv()
```



### 		-- server

> 单线程

```python
# This is the Server
import socket


# 创建socket对象
sk = socket.socket()

# 默认使用的是TCP编程，使用的端口作为TCP的端口
# sk.bind((IP地址，端口))
sk.bind(("127.0.0.1", 12345))

# 开启监听
sk.listen()

print("服务器准备完毕，等待客户端连接...")
# conn:你和客户端的连接
# address:客户端的IP和端口
conn, address = sk.accept()  # 等待客户端的连接
print("连接成功")

while 1:
    # 发送数据，数据必须是字节
    # conn.send("加密密码".encode("utf-8"))
    conn.send(input(">>> ").encode("utf-8"))

    # 服务器接受数据
    msg = conn.recv(1024)

    if msg.decode("utf-8").upper() == "Q":
        conn.send("q".encode("utf-8"))
        break

    print("服务器接受信息为：", msg.decode("utf-8"))

```





### 		-- client

> 单线程

```python
#   This is the Client
import socket


# 创建socket对象
sk = socket.socket()

# 主动连接服务器
sk.connect(("127.0.0.1", 12345))
print("客户端连接成功")

while 1:
    # 接收服务器数据，数据大小至多为1024字节
    r = sk.recv(1024)

    if r.decode("utf-8").upper() == "Q":
        sk.send("q".encode("utf-8"))
        break

    print("客户端接受信息为：", r.decode("utf-8"))
    # 向服务器发送消息
    # sk.send("解密密码完成".encode("utf-8"))
    sk.send(input(">>> ").encode("utf-8"))

```



### 		-- rise

> **server.py**

```python
# This is the Server
import socket

from socket_tools import MyRecvThread, MySendThread

# 创建socket对象
sk = socket.socket()

# 默认使用的是TCP编程，使用的端口作为TCP的端口
# sk.bind((IP地址，端口))
sk.bind(("127.0.0.1", 12345))

# 开启监听
sk.listen()

print("服务器准备完毕，等待客户端连接...")
# conn:你和客户端的连接
# address:客户端的IP和端口
conn, address = sk.accept()  # 等待客户端的连接
print("连接成功")

send = MySendThread(conn)
recv = MyRecvThread(conn)

send.start()
recv.start()


```



> **client.py**

```python
#   This is the Client
import socket

from socket_tools import MyRecvThread, MySendThread

# 创建socket对象
sk = socket.socket()

# 主动连接服务器
sk.connect(("127.0.0.1", 12345))
print("客户端连接成功")

send = MySendThread(sk)
recv = MyRecvThread(sk)

send.start()
recv.start()

```



> **socket_tools.py**

```python
from threading import Thread


class MySendThread(Thread):
    def __init__(self, conn):
        super(MySendThread, self).__init__()
        self.conn = conn

    def run(self) -> None:
        while 1:
            self.conn.send(input(">>> ").encode("utf-8"))


class MyRecvThread(Thread):
    def __init__(self, conn):
        super(MyRecvThread, self).__init__()
        self.conn = conn

    def run(self) -> None:
        while 1:
            msg = self.conn.recv(1024)
            print("接受信息为：", msg.decode("utf-8"))

```



### 		-- 黏包

> 上一次的数据和下一次的数据黏连在一起

> 解决黏包方法：
>
> 每发送一个数据包的时候，拆分成两个包
>
> 第一个包：告诉对方，我要发送的数据有多少
>
> 第二个包：真实的数据
>
> 总结：先告诉对方数据的边界在哪，后接收数据

> **server.py**

```python
import socket
import struct


sk = socket.socket()

sk.bind(("127.0.0.1", 12345))
sk.listen()

print("服务器准备完毕，等待客户端连接...")
conn, address = sk.accept()
print("连接成功")

try:
    while 1:
        msg = conn.recv(4)
        # 解析
        # 返回的是元组，数据在[0]
        msg_len_bs = struct.unpack("i", msg)[0]
        msg_data = conn.recv(msg_len_bs).decode("utf-8")
        print(msg_len_bs)
        print(msg_data)
except struct.error:
    pass

```



> **client.py**

```python
import socket
import struct


sk = socket.socket()

sk.connect(("127.0.0.1", 12345))
print("客户端连接成功")

for count in range(2):
    msg = "信息".encode("utf-8")
    # 将整数转换成4位数字节
    msg_len_bs = struct.pack("i", len(msg))

    sk.send(msg_len_bs)
    sk.send(msg)

```



### -- 文件上传

> 客户端向服务器上传图片

> **server.py**

```python
import socket
import struct
import json


def date_recv(recv_conn):
    msg_len_bs = recv_conn.recv(4)
    msg_len = struct.unpack("i", msg_len_bs)[0]
    result = recv_conn.recv(msg_len)
    return result


sk = socket.socket()
sk.bind(("127.0.0.1", 12345))
sk.listen()

print("服务器完毕")
conn, address = sk.accept()
print("连接成功")

msg_str = date_recv(conn).decode("utf-8")
# 将 str 转化成 dict 格式
msg_dict = json.loads(msg_str)

with open(msg_dict["name"], mode="wb") as f:
    while 1:
        msg = date_recv(conn)
        if msg == b"1111":
            break
        f.write(msg)

```

> **client.py**

```python
import os.path
import socket
import struct
import json


def data_send(send_sk, send_bs):

    msg_len = len(send_bs)
    send_sk.send(struct.pack("i", msg_len))
    send_sk.send(send_bs)


sk = socket.socket()
sk.connect(("127.0.0.1", 12345))
print("客户端连接成功")

# 先发送文件相关信息
data = {
    "name": "Kali.png",
    "size": os.path.getsize("Kali.png")
}
# 将dict转换成str格式
data_json = json.dumps(data)
data_send(sk, data_json.encode("utf-8"))

f = open("Kali.png", mode="rb")
for bs in f:
    data_send(sk, bs)
f.close()
data_send(sk, b"1111")

```



### -- socketserver

> 可以使用一个服务端连接多个客户端

> **server.py**

```python
# 使用socketserver来管理客户端的连接
import socketserver
import time


class MyRequestHandler(socketserver.BaseRequestHandler):

    def handle(self) -> None:  # 一旦有客户端连接，自动执行handle
        # 每个客户端连接，都是一个单独的线程
        print("有客户端连接")
        conn = self.request  # 与客户端的连接
        address = self.client_address   # 客户端ip地址
        while 1:
            conn.send(f"你的IP:{address}".encode("utf-8"))
            time.sleep(2)


if __name__ == '__main__':
    # ThreadingTCPServer中提供IP地址，端口，指定谁来处理请求[类]
    # TCPServer 只有前一个进程关闭才可以再进行连接，单线程
    # ThreadingTCPServer 每一个客户端是一个线程，多线程
    server = socketserver.ThreadingTCPServer(("127.0.0.1", 12345), MyRequestHandler)
    server.serve_forever()  # 启动

```

> **client.py**

```python
import socket


sk = socket.socket()
sk.connect(("127.0.0.1", 12345))

while 1:
    msg = sk.recv(1024)
    print(msg.decode("utf-8"))

```



## 	- UDP编程

> 特点：不连续的，不可靠的，效率较高.
>

> **不会出现黏包的问题**
>
> **创建socket的时候需要指定type = socket.SOCK_DGRAM** 
>
> 发送数据 sendto(信息，目标(ip, port))
>
> 接受数据 recvfrom(接收信息字节大小)



### -- server

```python
import socket


# SOCK_DGRAM发送的是(UDP)数据包
sk = socket.socket(type=socket.SOCK_DGRAM)

# UDP的端口
sk.bind(("127.0.0.1", 12345))

# 数据包中有[信息]和[客户端地址]
msg, client = sk.recvfrom(1024)
print(msg.decode("utf-8"), client)

sk.sendto("已收到:{}".format(msg.decode("utf-8")).encode("utf-8"), client)

```



### -- client

```python
import socket


sk = socket.socket(type=socket.SOCK_DGRAM)

# 发送UDP数据包
# sendto(发送信息， 发送目标[ip, port])
sk.sendto("information".encode("utf-8"), ("127.0.0.1", 12345))

msg, client = sk.recvfrom(1024)
print(msg.decode("utf-8"))

```



# Python连接Mysql数据库



## -pymysql

> pip install pymysql
>
> conn = pymysql.connect(host, port, user, password, database)
>
> cursor = conn.cursor(视情况选择不同的cursor)
>
> ​	默认的cursor适合增删改，DictCursor适合查询

> 准备sql，sql中的变量要使用%s来填充
>
> cursor.execute(sql, (sql需要的参数))

> 如果是增删改，则需要手动提交事务 (commit) ，如果出现错误，可以设置回滚 (rollabck)
>
> 如果时查找，则记得最后使用conn.close()进行关闭



### -- 增删改

```python
import pymysql


# 1.连接数据库
conn = pymysql.connect(
    host="127.0.0.1",
    port=3306,
    user="root",
    password="",
    database="ops"
)
print("数据库连接成功", conn)

try:
    # 2.创建cursor -> 可以运行sql语句
    cursor = conn.cursor()

    # 3.准备sql语句
    # 注意sql的写法，这种写法有被注入的风险
    # name, gender, salary = 'python', 'none', 150000
    # sql = f"insert into teacher(name, gender, salary) values ('{name}', '{gender}', '{salary}')"

    # 4.执行sql
    # cursor.execute(sql)
    # print("sql执行成功")

    # 更加安全的做法，推荐使用该方案
    name, gender, salary = 'python', 'none', 150000
    sql = f"insert into teacher(name, gender, salary) values (%s, %s, %s)"
    # 预处理，提前加载sql的逻辑，运行时把数据填进来
    cursor.execute(sql, (name, gender, salary))

    # 默认是开启了事务，而事务需要手动提交
    # 事务：一件事情的整体，并且提供了一次回滚(回到报错之前的状态)的机会
    # 例如：转账
    # 1.从我的账户扣钱
    # 2.向你的账户加钱
    # 3.记录转账细则

    conn.commit()   # 手动提交事务

except Exception as e:
    import traceback
    print("已记录本次错误", traceback.format_exc())
    conn.rollback()  # 事务的回滚，如果报错则回到执行操作之前的状态

```



### -- 默认的cursor查询

> 返回数据为元组

```python
import pymysql


# 默认的cursor查询
conn = pymysql.connect(
    host="127.0.0.1",
    port=3306,
    user="root",
    password="",
    database="ops"
)

cursor = conn.cursor()

sql = "select * from teacher"

cursor.execute(sql)

# 从cursor中获取到结果
# cursor.fetchone()     # 获取返回的第一条数据
# cursor.fetchmany(x)   # 获取返回的x条数据
# cursor.fetchall()     # 获取返回的所有数据

# one = cursor.fetchone()  # 每一条数据对应一个元组
# print(one[1], one[2])  # 需要用索引获取数据

large_tuple = cursor.fetchall()  # 元组套元组

# 获取每条数据的name, gender
for item in large_tuple:
    print(item[1], item[2])

```



### -- 更好的cursor查询

> 返回数据为列表与字典

```python
import pymysql
from pymysql.cursors import DictCursor


# 更好用的cursor查询
conn = pymysql.connect(
    host="127.0.0.1",
    port=3306,
    user="root",
    password="",
    database="ops"
)

# 使用DictCursor可以将返回的结果变为列表加字典
cursor = conn.cursor(DictCursor)

sql = "select * from teacher"

cursor.execute(sql)

# 每一条数据一个字典
print(cursor.fetchone())

# 多条数据, 返回格式为[{列名: 数据, 列名: 数据}, {}]
for item in cursor.fetchall():
    print(item["id"], item["name"], item["gender"], item["salary"])

```



### -- 简略的用户登录

```python
import pymysql
from pymysql.cursors import DictCursor


conn = pymysql.connect(
    host="127.0.0.1",
    port=3306,
    user="root",
    password="",
    database="ops"
)

user_name = input("用户名:")
user_password = input("密码:")

sql = "select * from user where username = %s and password = %s"

cursor = conn.cursor(DictCursor)
cursor.execute(sql, (user_name, user_password))

result = cursor.fetchone()

if result:
    print(f"登录成功:{user_name}")
else:
    print("用户名或密码输入错误")

# 记得关闭链接
conn.close()

```

