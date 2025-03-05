title: 基于Python的四程理解
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-03-04 01:35:00
---
#### 总述
网络编程，几乎所有网络的通信本质上都是通过socket模块实现。
并发编程，将串行的程序变为并发,提升代码执行的效率。
线程是计算机中可以被cpu调度的最小单元(真正在工作）。
进程是计算机资源分配的最小单元（进程为线程提供资源）。
一个进程中可以有多个线程,同一个进程中的线程可以共享此进程中的资源。
#### GIL
CPython解释器特有的全局解释器锁，让一个进程中同一个时刻只能有一个线程可以被CPU调用。
因为多进程的成本比多线程的成本高，所以建议计算密集型用多进程；IO密集型用多线程。
同时创建进程数建议与CPU个数相同，合理创建线程数
#### 多线程开发
```
import threading

lock_object = threading.RLock()
def task(arg):
    lock_object.acquire() # 加锁
	pass
    lock_object.release() # 释放锁
    
    # 创建一个Thread对象（线程），并封装线程被CPU调度时应该执行的任务和相关参数。
    t = threading.Thread(target=task,args=('xxx',))
    
    #设置为守护线程，主线程执行完毕后，子线程也自动关闭。
    #默认设置为非守护线程，主线程等待子线程，子线程执行完毕后，主线程才结束。
    #守护线程必须在start之前
    t.setDaemon(True/False)
    
    # 线程准备就绪（等待CPU调度），代码继续向下执行。
    t.start()
    # 等待当前线程的任务执行完毕后再向下继续执行。
    t.join()

print("继续执行...") # 主线程执行完所有代码，不结束（等待子线程）
```
#### 线程锁
在程序中手动加锁方法：同步锁Lock和递归锁RLock。
RLock支持多次申请锁和多次释放；Lock不支持。
死锁————由于竞争资源或者由于彼此通信而造成的一种阻塞的现象。
有些操作默认都是线程安全的，使用的时无需再通过锁再处理
```
import threading
lock_object = threading.RLock()

def task(arg):
    lock_object.acquire() # 加锁（令其它的等待）
	pass
    lock_object.release() # 释放锁（继续其它的任务）

//或者使用with lock_object自动执行 acquire 和 release
```
#### 线程池
```
import time
from concurrent.futures import ThreadPoolExecutor

# pool = ThreadPoolExecutor(100)
# pool.submit(函数名,参数1，参数2，参数...)


def task(video_url,num):
    print("开始执行任务", video_url)
    time.sleep(5)

# 创建线程池，最多维护10个线程。
pool = ThreadPoolExecutor(10)

url_list = []

for url in url_list:
    # 在线程池中提交一个任务，线程池中如果有空闲线程，则分配一个线程去执行，执行完毕后再将线程交还给线程池；如果没有空闲线程，则等待。
    pool.submit(task, url,2)
    
print("END")
```
#### 多进程开发
关于在Python中基于multiprocessiong模块操作的进程：
*fork*，【unix】【任意位置开始】【快】
*spawn*，【unix、win】【main代码块开始】【慢】
*forkserver*，【部分unix】【main代码块开始】
```
import multiprocessing
from multiprocessing import Process

multiprocessing.set_start_method("spawn")   #选择操作进程

def task():
	pass

if __name__ == '__main__':
    #当前进程准备就绪，等待被CPU调度（工作单元其实是进程中的线程）
    p1 = multiprocessing.Process(target=task)
    
    #设置为守护线程，主线程执行完毕后，子线程也自动关闭。
    #默认设置为非守护线程，主线程等待子线程，子线程执行完毕后，主线程才结束。
    #守护线程必须在start之前
    p.daemon(True/False)
    
    p1.start()
    #等待当前进程的任务执行完毕后再向下继续执行
    p.join()
    #这两行一定要放在初始化之后
```
#### 进程锁&进程池
***概念与用法与线程相似***
如果在进程池中要使用进程锁，则需要基于Manager中的Lock和RLock来实现。
#### 协程
协程（Coroutine），是程序员通过代码搞出来的一个东西（非真实存在）。
协程也可以被称为微线程，是一种用户态内的上下文切换技术。
简而言之，其实就是通过一个线程实现代码块相互切换执行（来回跳着执行）。
协程比线程更加节省开销，但协程的开发难度大一些。
```
<案例>
【greenlet】
from greenlet import greenlet

def func1():
    print(1)        # 第1步：输出 1
    gr2.switch()    # 第3步：切换到 func2 函数
    print(2)        # 第6步：输出 2
    gr2.switch()    # 第7步：切换到 func2 函数，从上一次执行的位置继续向后执行
def func2():
    print(3)        # 第4步：输出 3
    gr1.switch()    # 第5步：切换到 func1 函数，从上一次执行的位置继续向后执行
    print(4)        # 第8步：输出 4
gr1 = greenlet(func1)
gr2 = greenlet(func2)
gr1.switch() # 第1步：去执行 func1 函数


【yield】
from yield import yield

def func1():
    yield 1
    yield from func2()
    yield 2
def func2():
    yield 3
    yield 4
f1 = func1()
for item in f1:
    print(item)
```
让线程更加有意义:遇到IO请求时自动化切换（异步代码）
```
<案例>
import asyncio

async def func1():
    print(1)
    await asyncio.sleep(2)
    print(2)
    
async def func2():
    print(3)
    await asyncio.sleep(2)
    print(4)
    
tasks = [
    asyncio.ensure_future(func1()),
    asyncio.ensure_future(func2())
]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
```
写完这些已经很晚了，早上有时间再搞个异步代码和装饰器的文章吧。

### 每日一句（多久没写了？）
##### 其实“正常”才是无数误差交织中的碰巧完美。