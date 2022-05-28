## python异步

### 1.1 事件循环

理解为一个死循环，检测并执行某些代码。

```python
import asyncio
# 生成或获取一个事件循环
loop = asyncio.get_event_loop()
# 将任务放入任务列表
loop.run_until_complete(任务)
```



### 1.2 快速上手

协程函数，定义函数使用async def 关键字

```python
async def func():
    ...
```

协程对象，执行协程函数得到的协程对象

```python
result = func()  # 得到协程对象，不会执行函数
```

如果想要运行协程函数内部代码，必须要将协程对象交给事件循环

```python
# 生成或获取一个事件循环
loop = asyncio.get_event_loop()
# 将任务放入任务列表
loop.run_until_complete(result)
# 或者
asyncio.run(result) # python3.7+
```

### 1.3 await关键字

await 可等待的对象(协程对象，Future对象，task对象 -> io等待)

```python
import asyncio

async def func():
    ...
    response = await asyncio.sleep(2)
    print(response)
result = func()
asyncio.run(result)
```

```python
import asyncio
async def others():
    print("start")
    await asyncio.sleep(2)
    print("end")
    return 'return'
async def func():
    print("code inside coroutine func")
    response = awat others()
    print("io req over")

asyncio.run(func())
```

等待对象的值得到结果之后再往下进行

### 1.4 task对象

在事件循环中添加多个任务。

用于并发调度协程，通过``asyncio.create_task(协程对象)``'的方式创建task对象，这样可以让协程加入事件循环中等待被调度执行。除了使用``create_task``函数之外，还可用低层级的``loop.create_task()``或``ensure_future()``函数，不建议手动实例化task对象。

```python
import asyncio

async def func():
    print(1)
    await asyncio.sleep(1)
    print(2)
    return "return"

async def main():
    print("main开始")
    # 创建task对象，将当前执行的func函数任务添加到事件循环。
    task1 = asyncio.create_task(func())
    task2 = asyncio.create_task(func())
    print("main结束")
    # 当执行某协程遇到io操作时，会自动化切换成其他任务。
    # 此处的await时等待相对应的协程全部执行完毕并获取结果
    ret1 = await task1
    ret2 = await task2
    print(task1, task2)

asyncio.run(main())
```

```python
async def main():
    print("main开始")
    task_list = [
        asyncio.create_task(func()),
        asyncio.create_task(func())
        ]
    print("main结束")
    # done 为返回值的集合
    # timeout 最多等2秒
    done, pending = await asyncio.wait(task_list, timeout=2)
    print(done)
asyncio.run(main())
# OUTPUT:
'''
1
1
2
2
{<Task finished name='Task-3' coro=<func() done, defined at /home/naii/pypy/coroutine.py:4> result='return'>, <Task finished name='Task-2' coro=<func() done, defined at /home/naii/pypy/coroutine.py:4> result='return'>}
'''
```

```python
# 可在创建任务时指定name属性
asyncio.create_task(func(), name='n1')
```

task对象可将协程对象立即放入事件循环中

### 1.5 asyncio future对象

是task类的基类，task对象内部await结果的处理基于future对象来的

```python
import asyncio

async def func():
    # 获取当前事件循环
    loop = asyncio.get_running_loop()
    # 创建一个任务（future对象），这个任务什么都不做
    fut = loop.create_future()
    # 等待任务最终结果（future对象），没有结束一直等下去。
    await fut
asyncio.run(func())
# 此程序会一直阻塞下去
```

```python
import asyncio

async def set_after(fut):
    await asyncio.sleep(2)
    fut.set_result('666')

async def main():
    # 获取当前事件循环
    loop = asyncio.get_event_loop()
    # 创建一个任务（future对象），没有绑定任何行为，则这个任务不会结束
    fut = loop.create_future()
    # 创建一个任务（task对象）绑定了set_after函数，函数内部在2s后给fut赋值
    # 相当于手动给fut赋值，那么fut就会结束了
    await loop.create_task(set_after(fut))
    # 等待future对象获取最终结果
    data = await fut
    print(data)

asyncio.run(main())
```

### 1.6 concurrent.futures.future对象

使用线程池、进程池来实现异步操作时用到的对象。

```python
import time
from concurrent.futures import Future
from concurrent.futures.thread import ThreadPoolExecutor
from concurrent.futures.process import ProcessPoolExecutor

def func(value):
    time.sleep(1)
    print(value)
# 创建线程池
pool = ThreadPoolExecutor(max_workers=5)
# 或者 pool = ProcessPoolExecutor(max_workers=5) 创建进程池

for i in range(10):
    fut = pool.submit(func, i)
    print(fut)
```

例如：crm项目内部数据都是基于协程的+mysql（不支持）此时使用两者交叉使用

```python
import time 
import asyncio
import concurrent.futures

def func():
    # 某个耗时操作
    time.sleep(2)
    return "aa"

async def main():
    loop = asyncio.get_running_loop()
    
    # 1. Run in default loop's executor（默认ThreadPoolExecutor）
    # 第一步：内部会先调用 ThreadPoolExecutor的submit方法去线程池中申请一个线程去执行func函数
    # 并返回一个concurrent.futures.Future对象
    # 第二步：调用asyncio.wrap_future将concurrent.futures.Future对象包装为asyncio.Future对象
    # 因为concurrent.futures.Future对象不支持await语法，所以需要包装为asyncio.Future对象才能使用
    fut = loop.run_in_executor(None, func)
    result = await fut
    print('default thread pool', result)

    # 2. Run in a custom thread pool
    # with concurrent.futures.ThreadPoolExecutor() as pool:
    #   result = await loop.run_in_executor(pool.func1)
    # print(result)

asyncio.run(main())
```

案例：asyncio + 不支持异步的模块

```python
import asyncio
from pip import main
import requests

async def download(url):
    # 发送网络请求，下载图片（遇到网络下载图片的io请求，自动切换到其他任务）
    print("开始下载", url)
    
    loop = asyncio.get_event_loop()
    # requests 模块默认不支持异步操作，所以就使用线程池来配合实现了。
    future = loop.run_in_executor(None, requests.get, url)

    response = await future
    
    print("下载完成")
    # 图片保存到本地
    file_name = url.rsplit('_')[-1]
    with open(file_name, mode='wb') as f:
        f.write(response.content)
if __name__ == '__main__':
    url_list = [...]

    tasks = [download(url) for url in url_list]

    loop = asyncio.get_event_loop()
```

### 1.7 异步迭代器

什么是异步迭代器

实现了`__aiter__()`和`__anext__()`方法的对象，`__anext__`必须返回一个awaitable对象，async_for 会处理异步迭代器的`__anext__`()方法所返回的可等待对象，直至其引发一个StopAsyncIteratin异常

什么是异步可迭代对象

可在async_for  语句中被使用的对象，必须通过他的`__aiter__()`方法返回一个asyncchronous_iterator

```python
import asyncio

class Reader(object):
    # 自定义异步迭代器（同时也是异步可迭代对象）
    def __init__(self) -> None:
        self.count = 0
        
    async def readline(self):
        # await asyncio.sleep(1)
        self.count += 1
        if self.count == 100:
            return None
        
        return self.count
    
    def __aiter__(self):
        return self
    
    async def __anext__(self):
        val = await self.readline()
        if val == None:
            raise StopAsyncIteration
        return val
    
async def func():
    obj = Reader()
    async for item in obj:
        print(item)

asyncio.run(func())
```

### 1.8 异步上下文管理

这种对象通过定义`__aenter__()`和`__aexit__()`方法来对`async_with`语句中的环境进行控制。

```python
import asyncio


class AsyncContextManager:
    def __init__(self) -> None:
        self.conn = conn
        
    async def do_something(self):
        # 例如：异步操作数据库
        return 666
    async def __aenter__(self):
        # 异步链接数据库
        self.conn = await asyncio.sleep(1)
        return self
    
    async def __aexit__(self, exc_type, exc, tb):
        # 异步关闭数据库
        await asyncio.sleep(1)
async def func():       
	obj = AsyncContextManager()
	async with obj as f:
    	result = await f.do_something()
    	print(result)
asyncio.run(func())
```

### 1.9 uvloop

是asyncio事件循环的替代方案。uvloop事件循环的效率>默认asyncio的事件循环。

安装

```
pip install uvloop
```

```python
import asyncio
import uvloop
asyncio.set_event_loop_policy(uvloop.EventLoopPolicy())

# 编写asyncio的代码，没有变化
# 内部的事件循环会自动变为uvloop
asyncio.run(...)
```

注意：一个asgi -> `uvicorn` django3 fastapi内部使用uvloop

## 实战

### 2.1 异步操作redis

两台主机之间，操作redis时存在链接/操作/断开都是网络io

```
pip install aioredis
```

```python
import asyncio
import aioredis

async def executeimport asyncio
import aioredis

async def execute(address, password):
    print('开始执行', address)
    # 网络io操作：创建redis链接
    redis = await aioredis.create_redis(address, password=password)
    # 网络io操作：在redis中设置哈希值car，内部再设置三个键值对，即：
    # redis = {car: {key1:1, key2:2, key3:3}}
    await redis.hmset_dict('car', key1=1, key2=2, key3=3)

    # 网络io操作：去redis中获取值
    result = await redis.hgetall("car", encoding='utf-8')
    print(resut)

    redis.close()
    # 网络io操作：关闭redis链接
    await redis.wait_closed()
    
asyncio.run( execute('...', '...') )
```

```python
task_list = [
    execute(..., ...), # 不能在此处创建task对象，还未创建事件循环
    execute(..., ...)
]
asyncio.run(asyncio.wait(task_list))
```

### 2.2 异步操作mysql

```
pip install aiomysql
```

```python
import asyncio
import aiomysql

async def execute():
    # 网络io操作：链接mysql
    conn = await aiomysql.connect(host="127.0.0.1",
                                  port=3306,
                                  user="root",
                                  password="123",
                                  db='mysql')
    # 网络io操作：创建cursor
    cur = await conn.cursor()
    # 网络io操作：执行sql
    await cur.execute("select Host.User from user")
    # 网络io操作：获取sql结果
    result = cur.fetchall()
    print(result)
    # 网络io操作：关闭链接
    await cur.close()
    conn.close()

asyncio.run(execute())
```

链接多个mysql

```python
task_list = [
    execute(..., ...),
    execute(..., ...)
]
asyncio.run(asyncio.wait(task_list))
```

### 2.3 fastapi 框架异步

安装

```
pip install fastapi
pip install uvicorn # asgi
```

示例：

```python
# 同步写法
import uvicorn
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def index():
    # 普通操作接口
    return {"message": "hello"}
#          脚本名称:app
uvicorn.run("luffy:app", host="127.0.0.1", port=5000, log_level="info")
```

```python
# 异步写法
@app.get("/read")
async def read():
    # 异步操作接口
    print("收到请求")
    # 某个操作
    await asyncio.sleep(3)
    return 666
```

redis链接池

```python
REDIS_POOL = aioredis.ConnectionsPoll("redis://../", password="", minsize=1, maxsize=10)
@app.get('/redis')
async def redis_read():
    #  连接池获取一个连接
    conn = await REDIS_POOL.acquire()
    redis = Redis(conn)
    # 设置值
    await redis.hmset_dict('car', key1=1, key2=2)
    # 读取值
    result = await redis.hgetall('car', encoding='utf-8')
    print(redult)
    # 连接归还连接池
    REDIS_POOL.release(conn)
    return result
```

### 2.4 异步爬虫

```
pip install aiohttp
```

```python
import aiohttp
import asyncio

async def fetch(session, url):
    print("发送请求", url)
    async with session.get(url, verfy_ssl=False) as response:
        text = await response.text()
        print("得到结果", url, len(text))

async def main():
    async with aiohttp.ClientSession() as session:
        url_list = [
            ...,
            ...
        ]
        tasks = [asyncio.create_task(fetch(session, url)) for url in url_list]
        done, pending = await asyncio.wait(tasks)
    
asyncio.run(main())
```

