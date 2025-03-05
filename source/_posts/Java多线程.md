title: Java多线程
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-12-13 15:44:00
---
<a href="https://smms.app/image/OWJiKn1hyEl3F9P" target="_blank"><img src="https://s2.loli.net/2023/12/14/OWJiKn1hyEl3F9P.png" ></a>

## 多线程概念
<a href="https://smms.app/image/1QwXFzdknWJygA3" target="_blank"><img src="https://s2.loli.net/2023/12/14/1QwXFzdknWJygA3.jpg" ></a>
- 程序是指令和数据的集合
- 进程是执行程序的一次过程
- 线程是CPU调度执行的单位，无法人为干预
- 一个进程包括多个线程
- 分为真实多线程和模拟多线程

## 创建线程三种方式
### 【一】继承Thread类
1. 自定义线程类继承Thread类
2. 重写run()方法，编写线程执行体
3. 创建线程对象，调用start()方法启动线程

```Java
public class Main {
    public static void main(String[] args) {
        Thread t = new MyThread();   // 创建线程对象
        t.start();  // 调用start()方法启动线程
    }
}

class MyThread extends Thread {    // 自定义线程类继承Thread类
    @Override
    public void run() {    // 重写run()方法，编写线程执行体
        System.out.println("start new thread!");
    }
}
```


### 【二】实现Runnable接口
1. 定义MyRunnable类实现Runnable接口
2. 实现run()方法，编写线程执行体
3. 创建线程对象，调用start()方法启动线程
4. 推荐使用Runnable对象，因为Java单继承的局限性

```Java
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(new MyRunnable());   // 创建线程对象
        t.start(); // 调用start()方法启动线程
    }
}

class MyRunnable implements Runnable {   // 定义MyRunnable类实现Runnable接口
    @Override
    public void run() {   // 实现run()方法，编写线程执行体
        System.out.println("start new thread!");
    }
}
```


### 【三】通过 Callable 和 Future 创建线程
1. 实现Callable接口，需要返回值类型
2. 重写call方法，需要抛出异常
3. 创建目标对象
4. 创建执行服务:ExecutorService ser = Executors.newFixedThreadPool(1);
5. 提交执行: Future< Boolean > result1 = ser.submit(t1);
6. 获取结果: boolean r1 = result1.get()
7. 关闭服务: ser.shutdownNow();
8. 上两个方法在执行完任务之后无法获取执行结果，执行失败也不会抛出异常（得要通过共享变量或者使用线程通信的方式才能实现）
9. Callable接口代表一段可以调用并返回结果的代码;Future接口表示异步任务，是还没有完成的任务给出的未来结果。所以说Callable用于产生结果，Future用于获取结果

```Java
import java.util.concurrent.*;

public class java01 {
    ExecutorService executorService = Executors.newCachedThreadPool();
    Future<Integer> submit = executorService.submit(new Callable<Integer>() {
        @Override
        public Integer call() throws Exception {
            return null;
        }
    });
    Callable<Integer> callable = new Callable<Integer>() {
        @Override
        public Integer call() throws Exception {
            return null;
        }
    };
    FutureTask<Integer> integerFutureTask = new FutureTask<>(callable);
}

new Thread(integerFutureTask).start();
```

  **推荐使用实现Runnable接口的方法来创建线程，可以避免Java单继承的局限性，同时操作同个对象**
  **但是正因为同时操作了同个对象，导致了线程安全问题**

## Java三种代理模式
  代理模式是一种设计模式，提供了对目标对象额外的访问方式，即通过代理对象访问目标对象，这样可以在不修改原目标对象的前提下，提供额外的功能操作，扩展目标对象的功能。

  简言之，代理模式就是设置一个中间代理来控制访问原目标对象，以达到增强原对象的功能和简化访问方式。

### **【静态代理】**
这种代理方式需要**代理对象和目标对象实现一样的接口**。
优点：可以在不修改目标对象的前提下扩展目标对象的功能。
缺点：

    冗余。由于代理对象要实现与目标对象一致的接口，会产生过多的代理类。
    不易维护。一旦接口增加方法，目标对象与代理对象都要进行修改。

```Java

public interface IUserDao {  // 接口类： IUserDao
    public void save();  // 定义接口方法
}

public class UserDao implements IUserDao{  // 目标对象：UserDao

    @Override
    public void save() {  // 实现目标对象的接口方法
        System.out.println("保存数据");
    }
}
 
public class UserDaoProxy implements IUserDao{ // 静态代理对象：UserDapProxy 需要实现IUserDao接口！
    private IUserDao target;
    public UserDaoProxy(IUserDao target) {
        this.target = target;
    }
    
    @Override
    public void save() {  // 实现代理对象的接口方法
        System.out.println("开启事务");  //扩展了额外功能
        target.save();
        System.out.println("提交事务");
    }
}

public class StaticUserProxy {  // 测试类：TestProxy
    @Test
    public void testStaticProxy(){
        //目标对象
        IUserDao target = new UserDao();
        //代理对象
        UserDaoProxy proxy = new UserDaoProxy(target);
        proxy.save();  // 调用代理对象的方法
    }
}

输出结果:
  开启事务
  保存数据
  提交事务
```

### **【动态代理】**
  动态代理利用了JDK API，动态地在内存中构建代理对象，从而实现对目标对象的代理功能。动态代理又被称为JDK代理或接口代理。

静态代理与动态代理的区别主要在：

    静态代理在编译时就已经实现，编译完成后代理类是一个实际的class文件
    动态代理是在运行时动态生成的，即编译完成后没有实际的class文件，而是在运行时动态生成类字节码，并加载到JVM中

特点：
  动态代理对象不需要实现接口，但是要求**目标对象必须实现接口**，否则不能使用动态代理。

```Java

public interface IUserDao {  // 接口类： IUserDao
    public void save();  // 定义接口方法
}

public class UserDao implements IUserDao{  // 目标对象：UserDao

    @Override
    public void save() {  // 实现目标对象的接口方法
        System.out.println("保存数据");
    }
}

public class ProxyFactory {

    private Object target;// 维护一个目标对象

    public ProxyFactory(Object target) {
        this.target = target;
    }

    // 为目标对象生成代理对象
    public Object getProxyInstance() {
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(),
                new InvocationHandler() {

                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("开启事务");

                        // 执行目标对象方法
                        Object returnValue = method.invoke(target, args);

                        System.out.println("提交事务");
                        return null;
                    }
                });
    }
}

public class TestProxy {
    @Test
    public void testDynamicProxy (){
        IUserDao target = new UserDao();
        System.out.println(target.getClass());  //输出目标对象信息
        IUserDao proxy = (IUserDao) new ProxyFactory(target).getProxyInstance();
        System.out.println(proxy.getClass());  //输出代理对象信息
        proxy.save();  //执行代理方法
    }
}

输出结果:
  开启事务
  保存数据
  提交事务
```

### **【cglib代理】**
  cglib 是一个第三方代码生成类库，运行时在内存中动态生成一个子类对象从而实现对目标对象功能的扩展。

cglib特点：**无需实现接口**

    JDK的动态代理有一个限制，就是使用动态代理的对象必须实现一个或多个接口。
    如果想代理没有实现接口的类，就可以使用CGLIB实现。
    CGLIB是一个强大的高性能的代码生成包，它可以在运行期扩展Java类与实现Java接口。
    它广泛的被许多AOP的框架使用，例如Spring AOP和dynaop，为他们提供方法的interception（拦截）。
    CGLIB包的底层是通过使用一个小而快的字节码处理框架ASM，来转换字节码并生成新的类。
    不鼓励直接使用ASM，因为它需要你对JVM内部结构包括class文件的格式和指令集都很熟悉。

cglib与动态代理最大的区别就是

    使用动态代理的对象必须实现一个或多个接口
    使用cglib代理的对象则无需实现接口，达到代理类无侵入。

```Java

public class UserDao{

    public void save() {
        System.out.println("保存数据");
    }
}

public class ProxyFactory implements MethodInterceptor{

    private Object target;//维护一个目标对象
    public ProxyFactory(Object target) {
        this.target = target;
    }
    
    //为目标对象生成代理对象
    public Object getProxyInstance() {
        //工具类
        Enhancer en = new Enhancer();
        //设置父类
        en.setSuperclass(target.getClass());
        //设置回调函数
        en.setCallback(this);
        //创建子类对象代理
        return en.create();
    }

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        System.out.println("开启事务");
        // 执行目标对象的方法
        Object returnValue = method.invoke(target, args);
        System.out.println("关闭事务");
        return null;
    }
}

public class TestProxy {

    @Test
    public void testCglibProxy(){
        //目标对象
        UserDao target = new UserDao();
        System.out.println(target.getClass());
        //代理对象
        UserDao proxy = (UserDao) new ProxyFactory(target).getProxyInstance();
        System.out.println(proxy.getClass());
        //执行代理对象方法
        proxy.save();
    }
}

输出结果:
  开启事务
  保存数据
  关闭事务
```

### 总结
1. 静态代理实现较简单，只要代理对象对目标对象进行包装，即可实现增强功能，但静态代理只能为一个目标对象服务，如果目标对象过多，则会产生很多代理类。
2. JDK动态代理需要目标对象实现业务接口，代理类只需实现InvocationHandler接口。
3. 静态代理在编译时产生class字节码文件，可以直接使用，效率高。
4. 动态代理必须实现InvocationHandler接口，通过反射代理方法，比较消耗系统性能，但可以减少代理类的数量，使用更灵活。
5. cglib代理无需实现接口，通过生成类字节码实现代理，比反射稍快，不存在性能问题，但cglib会继承目标对象，需要重写方法，所以目标对象不能为final类。

## Lambda表达式
使用Lambda表达式替换单方法接口：
```Java

Arrays.sort(array, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});

上面的可变为下面的形式

Arrays.sort(array, (s1, s2) -> {
    return s1.compareTo(s2);
});

参数是(s1, s2)，参数类型可以省略，因为编译器可以自动推断出String类型。-> { ... }表示方法体，所有代码写在内部即可。
```
如果只有一行return xxx的代码，完全可以用更简单的写法：
```Java

Arrays.sort(array, (s1, s2) -> s1.compareTo(s2));

返回值的类型也是由编译器自动推断的，这里推断出的返回值是int，因此，只要返回int，编译器就不会报错。
```

## 线程五大状态
<a href="https://smms.app/image/ahJjMVtcqdRIEPf" target="_blank"><img src="https://s2.loli.net/2023/12/14/ahJjMVtcqdRIEPf.jpg" ></a>
<a href="https://smms.app/image/c8B1KWYHNpOdmb2" target="_blank"><img src="https://s2.loli.net/2023/12/14/c8B1KWYHNpOdmb2.jpg" ></a>

### 线程停止
不推荐使用JDK提供的 stop()、destroy()方法。
推荐线程自己停止下来，使用一个标志位进行终止变量（当flag=false终止线程运行）

### 线程休眠
  sleep(时间)指定当前线程阻塞的毫秒数
  sleep存在异常InterruptedException;
  sleep时间达到后线程进入就绪状态
  sleep可以模拟网络延时，倒计时等
  每一个对象都有一个锁，sleep不会释放锁

### 线程礼让
  礼让线程，让当前正在执行的线程暂停，但不阻塞
  将线程从运行状态转为就绪状态
  让cpu重新调度，礼让不一定成功!看CPU心情

### 线程强制执行
  Join合并线程，待此线程执行完成后，再执行其他线程，其他线程阻塞

### 线程优先级
  Java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度哪个线程来执行线程的优先级用数字表示，范围从1~10.
`Thread.MIN PRIORITY = 1;`
使用以下方式改变或获取优先级
```
getPriority() . setPriority(int xxx)
```

### 守护线程
  线程分为用户线程和守护线程
  虚拟机必须确保**用户线程**执行完毕
  虚拟机不用等待**守护线程**执行完毕
```
thread.setDaemon(true); //默认是false表示是用户线程 ，正常的线程都是用户线程
```

## 线程同步
实现要求：队列+锁
由于同一进程的多个线程共享同一块存储空间，导致访问冲突问题
为了保证数据在方法中被访问时的正确性，在访问时加入锁机制`synchronized`
加锁释放锁会导致比较多的上下文切换和调度延时起性能问题
但是一个优先级高的线程等待一个优先级低的线程释放锁会导致优先级倒置，引起性能问题

### 同步方法和同步块
同步方法: 
`public synchronized void method(int args) {}`
  每个对象对应一把锁每个synchronized方法都必须获得调用该方法的对象的锁才能执行
  缺陷:若将一个大的方法申明为synchronized 将会影响效率

同步块: 
`synchronized (obj) {}`
  obj 可以是任何对象，但是推荐使用共享资源作为同步监视器（obj）
  同步方法中无需指定同步监视器（obj），因为同步方法的同步监视器就是this，就是这个对象本身
1. 第一个线程访问，锁定同步监视器，执行其中代码
2. 第二个线程访问，发现同步监视器被锁定，无法访问
3. 第一个线程访问完毕，解锁同步监视器
4. 第二个线程访问，发现同步监视器没有锁，然后锁定并访问

  对于普通同步方法，锁是当前实例对象。如果有多个实例,那么锁对象必然不同无法实现同步。
  对于静态同步方法，锁是当前类的Class对象。有多个实例但是锁对象是相同的,可以完成同步。
  对于同步方法块，锁是Synchonized括号里配置的对象。如当前类的class只有一个,锁对象相同 也能实现同步。

## 锁
### 死锁
  多个线程各自占有一些共享资源，并且互相等待其他线程占有的资源才能运行
  而某一个同步块同时拥有“两个以上对象的锁”导致两个或者多个线程都在等待对方释放资源，都停止执行的情形时，就可能会发生“死锁”的问题

产生死锁的四个必要条件：
1. 互斥条件:一个资源每次只能被一个进程使用。
2. 请求与保持条件:一个进程因请求资源而阻塞时，对已获得的资源保持不放
3. 不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺
4. 循环等待条件:若千进程之间形成一种头尾相接的循环等待资源关系

  只要打破以上的任意一个条件就能解除死锁。

### Lock锁
  从JDK5.0开始Java提供了更强大的线程同步机制-通过显式定义同步锁对象来实现同步。同步锁使用Lock对象实现。
  锁提供了对共享资源的独占访问，每次只能有一个线程对Lock对象加锁，线程开始访问共享资源之前应先获得Lock对象
  ReentrantLock（可重入锁）拥有与synchronized 相同的并发性和内存语义，在实现线程安全的控制中较常用，可以显式加锁、释放锁。

使用示例：
```Java

public void testMethod() {
	lock.lock();
	try {
		System.out.println("xxxxxxxxxxxxxxxxx");
	} finally {
		lock.unlock();
	}
}
```

### Lock与Synchronized的比较
- Lock是显式锁(手动开启和关闭锁，别忘记关闭锁)synchronized是隐式锁，出了作用域自动释放
- Lock只有代码块锁，synchronized有代码块锁和方法锁使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性(提供更多的子类)
- **优先使用顺序:**
- Lock >同步代码块 (已经进入了方法体，分配了相应资源)>同步方法 (在方法体之外)


## 生产者消费者问题
### 问题概述
  生产者和消费者共享同一个资源，并且生产者和消费者之间相互依赖，互为条件
  **对于生产者：**没有生产产品之前，要通知消费者等待而生产了产品之后，又需要马上通知消费者消费
  **对于消费者：**在消费之后，要通知生产者已经结束消费，需要生产新的产品以供消费
- synchronized 可阻止并发更新同一个共享资源，实现了同步
- synchronized 不能用来实现不同线程之间的消息传递(通信)

解决通信问题的方法，都是Obj类方法，只能在同步方法或者同步方法块内。

| 方法名 | 作用   |
|------|--------|
| wait()  | 表示线程一直等待，直到其他线程通知，与sleep不同会释放锁  |
| wait(long timeout)  | 指定等待的毫秒数  |
| notify()  | 唤醒一个处于等待状态的线程  |
| notifyAll()  | 唤醒同一个对象上所有调用wait()方法的线程，优先级别高的线程优先调度  |

### 管程法
  使用wait()和notify()方法的实现

**生产者:** 负责生产数据的模块(可能是方法，对象，线程，进程)
**消费者:** 负责处理数据的模块(可能是方法，对象，线程，进程)
**缓冲区:** 生产者将生产好的数据放入缓冲区，消费者从缓冲区拿出数据

  缓冲区满或者空时都调用wait()方法等待，当生产者生产了一个产品或者消费者消费了一个产品之后会唤醒所有线程。

### 可重入锁ReentrantLock的实现
  可重入锁，也叫做递归锁，指的是同一线程 外层函数获得锁之后，内层递归函数仍然有获取该锁的代码，但不受影响。
  锁维护一个与获取锁相关的计数器，如果拥有锁的某个线程再次得到锁，那么获取计数器就加1，函数调用结束计数器就减1。
  锁需要被释放两次才能获得真正释放，已经获取锁的线程进入其他需要相同锁的同步代码块不会被阻塞。

### 阻塞队列BlockingQueue的实现
  BlockinoQueue是一个已经在内部实现了同步的队列，实现方式采用的是await()/signal()方法。它可以在生成对象时指定容量大小，用于阻塞操作的是put()和take()方法。
put()方法: 类似于我们上面的生产者线程，容量达到最大时，自动阻塞。
take(方法: 类似于我们上面的消费者线程，容量为0时，自动阻寒。
  当一个线程对已经满了的阻塞队列进行入队操作时会阻塞，除非有另外一个线程进行了出队操作，当一个线程对一个空的阻塞队列进行出队操作时也会阻塞，除非有另外一个线程进行了入队操作。
从上可知，阻塞队列是线程安全的。

### 信号量 Semaphore 的实现
  Semaphore是一种基于计数的信号量，它可以设定一个阀值。
  当多个线程竞争获取许可信号，做完自己的申请后归还，超过闻值后，线程申请许可信号将会被阻塞。
  Semaphore可以用来构建一些对象池，资源池之类的，比如数据库连接池，我们也可以创建计数为的Semaphore，将其作为一种类似互斥锁的机制，这也叫二元信号量，表示两种互斥状态。
  计数为0的Semaphore是可以release的，然后就可以acquire (即一开始使线程阻塞从而完成其他执行)。

### 管道输入输出流实现
  先创建一个管道输入流和管道输出流，然后将输入流和输出流进行连接
  用生产者线程往管道输出流中写入数据，消费者在管道输入流中读取数据，这样就可以实现了不同线程间的相互通讯
  但是这种方式在生产者和生产者、消费者和消费者之间不能保证同步，也就是说在一个生产者和一个消费者的情况下是可以生产者和消费者之间交替运行的，多个生成者和多个消费者者之间则不行

## 线程池
  **背景:** 经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大。
  **思路:** 提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中可以避免频繁创建销毁、实现重复利用。
**好处:**
1. 提高响应速度(减少了创建新线程的时间)
2. 降低资源消耗(重复利用线程池中线程，不需要每次都创建)
3. 便于线程管理
`corePoolSize`: 核心池的大小
`maximumPoolSize`: 最大线程数
`keepAliveTime`: 线程没有任务时最多保持多长时间后会终止

```Java

private static void createCachedThreadPool() {
        ExecutorService executorService = Executors.newCachedThreadPool();  // 可缓存的线程池，若线程数超过处理所需，缓存一段时间后会回收，若线程数不够，则新建线程。
        ExecutorService executorService = Executors.newFixedThreadPool(3);  // 固定大小的线程池，可控制并发的线程数，超出的线程会在队列中等待。
        ScheduledExecutorService executorService = Executors.newScheduledThreadPool(3);  // 周期性的线程池，支持定时及周期性执行任务。
        ExecutorService executorService = Executors.newSingleThreadExecutor();  // 单线程的线程池，可保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。
        //   使用ThreadPoolExecutor自行定义线程池
        for (int i = 0; i < 10; i++) {
            final int index = i;
            executorService.execute(() -> {
                // 获取线程名称,默认格式:pool-1-thread-1
                System.out.println(DateUtil.now() + " " + Thread.currentThread().getName() + " " + index);
                // 等待2秒
                sleep(2000);
            });
        }
}
```
  应该使用ThreadPoolExecutor类来创建线程池，根据自己需要的场景来创建一个合适的线程池。


## 后记
  很尴尬这是这个学期的第一篇博客文章，有一种一个学期没学啥东西的感觉
**orz**能鸽这么久确实是没想到   
  接下来可能会整一些java的东西，然后有计划写算法或者安全开发这些东西。


<a href="https://smms.app/image/hDei8vSbxJVIqto" target="_blank"><img src="https://s2.loli.net/2023/12/14/hDei8vSbxJVIqto.jpg" width = "500" ></a>