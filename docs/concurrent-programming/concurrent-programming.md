
## 并发编程

[参考](http://python3-cookbook-personal.readthedocs.io/zh_CN/latest/chapters/p12_concurrency.html)
[廖雪峰python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143208573480558080fa77514407cb23834c78c6c7309000)

### 理解协程

Python对协程的支持是通过generator实现的。
在generator中，我们不但可以通过for循环来迭代，还可以不断调用next()函数获取由yield语句返回的下一个值。
但是Python的__yield不但可以返回一个值，它还可以接收调用者发出的参数__。
来看例子：
传统的`生产者-消费者`模型是一个线程写消息，一个线程取消息，通过锁机制控制队列和等待，但一不小心就可能死锁。
如果改用协程，生产者生产消息后，直接通过yield跳转到消费者开始执行，待消费者执行完毕后，切换回生产者继续生产，效率极高：


```python
#%%
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
#[PRODUCER] Producing 1...
#[CONSUMER] Consuming 1...
#[PRODUCER] Consumer return: 200 OK
#[PRODUCER] Producing 2...
#[CONSUMER] Consuming 2...
#[PRODUCER] Consumer return: 200 OK
#[PRODUCER] Producing 3...
#[CONSUMER] Consuming 3...
#[PRODUCER] Consumer return: 200 OK
#[PRODUCER] Producing 4...
#[CONSUMER] Consuming 4...
#[PRODUCER] Consumer return: 200 OK
#[PRODUCER] Producing 5...
#[CONSUMER] Consuming 5...
#[PRODUCER] Consumer return: 200 OK
```


## 携程库asyncio

注意到`consumer`函数是一个`generator`，把一个`consumer`传入`produce`后：
首先调用`c.send(None)`启动生成器；
然后，一旦生产了东西，通过`c.send(n)`切换到`consumer`执行；
`consumer`通过`yield`拿到消息，处理，又通过`yield`把结果传回；
`produce`拿到`consumer`处理的结果，继续生产下一条消息；
`produce`决定不生产了，通过`c.close()`关闭`consumer`，整个过程结束。
整个流程无锁，由一个线程执行，produce和consumer协作完成任务，所以称为“协程”，而非线程的抢占式多任务。
最后套用Donald Knuth的一句话总结协程的特点：
“子程序就是协程的一种特例。”
asyncio是Python 3.4版本引入的标准库，直接内置了对异步IO的支持。
asyncio的编程模型就是一个消息循环。我们从asyncio模块中直接获取一个EventLoop的引用，然后把需要执行的协程扔到EventLoop中执行，就实现了异步IO。
用asyncio实现Hello world代码如下：


```python
#%%
import asyncio

@asyncio.coroutine
def hello():
    print("Hello world!")
    #异步调用asyncio.sleep(1)
    r = yield from asyncio.sleep(1)
    print("Hello again!")

#获取EventLoop:
loop = asyncio.get_event_loop()
#执行coroutine
loop.run_until_complete(hello())
loop.close()
```


### async/await


### aiohttp



```python
#
```

## C++

c/c++不直接支持协程语义，但有不少开源的协程库，如：
Protothreads：一个“蝇量级” C 语言协程库
libco:来自腾讯的开源协程库libco介绍，官网
coroutine:云风的一个C语言同步协程库,详细信息
