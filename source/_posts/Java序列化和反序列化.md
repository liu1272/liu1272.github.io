title: Java序列化和反序列化
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-12-19 18:53:00
---
### 序列化与反序列化概念
  Java序列化是指把Java对象转换为字节序列的过程；
  Java反序列化是指把字节序列恢复为Java对象的过程；

  Java的序列化技术就是把对象转换成一串由二进制字节组成的数组，然后将这二进制数据保存在磁盘或传输网络。
  而后需要用到这对象时，磁盘或者网络接收者可以通过反序列化得到此对象，达到对象持久化的目的。
反序列化条件：
- 该类必须实现 `java.io.Serializable` 对象
- 该类的所有属性必须是可序列化的。如果有一个属性不是可序列化的，则该属性必须注明是短暂的（注明是瞬态的，使用`transient`）
序列化过程：
- 序列化：将 `OutputStream` 封装在 `ObjectOutputStream` 内，然后调用 `writeObject `即可
- 反序列化：将 `InputStream` 封装在 `ObjectInputStream` 内，然后调用 `readObject `即可

### 序列化与反序列化操作
#### 序列化
  PHP序列化字符串基本上是可人读的，而且对于类对象来说，字段等成员属性的序列化顺序与定义顺序一致；PHP经过序列化生成类似`O:17:"SerializationDemo":2:...`的字符串而对象经过Java序列化后得到的则是一个二进制串。
##### 序列化过程
1. ObjectOutputStream实例初始化时，将魔术头和版本号写入bout （BlockDataOutputStream类型） 中
2. 调用ObjectOutputStream.writeObject()开始写对象数据
	- 写入对象类型标识
	- writeClassDesc()进入分支writeNonProxyDesc()写入类描述数据
	- writeSerialData()写入对象的序列化数据
	- 写入类描述符标识
	- 写入类名
	- 写入SUID（当SUID为空时，会进行计算并赋值）
	- 计算并写入序列化属性标志位
	- 写入字段信息数据
	- 写入Block Data结束标识
	- 写入父类描述数据
	- 若类自定义了writeObject()，则调用该方法写对象，否则调用defaultWriteFields()写入对象的字段数据 （若是非原始类型，则递归处理子对象）
	- ObjectStreamClass.lookup()封装待序列化的类描述 （返回ObjectStreamClass类型） ，获取包括类名、自定义serialVersionUID、可序列化字段 （返回ObjectStreamField类型） 和构造方法，以及writeObject、readObject方法等
	- writeOrdinaryObject()写入对象数据

#### 反序列化
##### 反序列化过程
  Java程序中类ObjectInputStream的readObject方法被用来将数据流反序列化为对象，如果流中的对象是class，则它的ObjectStreamClass描述符会被读取，并返回相应的class对象，ObjectStreamClass包含了类的名称及serialVersionUID。

##### `*serialVersionUID`
  serialVersionUID适用于java序列化机制。简单来说，JAVA序列化的机制是通过serialVersionUID判断类的
  serialVersionUID来验证的版本一致的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较。
  如果相同说明是一致的，可以进行反序列化，否则会出现反序列化版本一致的异常，即是InvalidCastException。

### 反序列化漏洞的基本原理
  在Java反序列化中，会调用被反序列化的readObject方法，当readObject方法被重写不当时产生漏洞此处重写了readObject方法，执行`Runtime.getRuntime().exec()`
  defaultReadObject方法为ObjectInputStream中执行readObject后的默认执行方法
运行流程：
  1.myObj对象序列化进object文件
  2.object反序列化对象->调用readObject方法->执行`Runtime.getRuntime().exec("calc.exe");`


<a href="https://smms.app/image/lV8EkChBzjDSPUs" target="_blank"><img src="https://s2.loli.net/2023/12/20/lV8EkChBzjDSPUs.jpg" alt="Java反射安全.png"></a>

也就是`field.setAccessible(true);`所造成的问题。
再加上以下方法就能造成安全问题
```
forName  获取类
newInstance  实例化类对象
getMethod   获取函数
invoke   执行函数
writeObject()   序列化，将Object输出成Byte流
readObject()   反序列化，将Byte流输出成Object
```
  例如：利用反射机制，重写readObject方法，加入能够进行命令执行的函数Runtime.getRuntime()，执行calc.exe命令调出计算器

```Java

import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

//创建一个可序列化的接口类
public class CommandExecution implements Serializable {
    public static void main(String[] args) throws Exception {
        // 创建一个恶意的对象
        EvilObject evilObject = new EvilObject();

        // 序列化恶意对象
        byte[] serializedData = serialize(evilObject);

        // 反序列化恶意对象
        deserialize(serializedData);
    }

    // 序列化对象
    private static byte[] serialize(Object obj) throws IOException {
        ObjectOutputStream out = new ObjectOutputStream(System.out);
        out.writeObject(obj);
        out.close();
        return null;
    }

    // 反序列化对象
    private static void deserialize(byte[] serializedData) throws Exception {
        ObjectInputStream in = new ObjectInputStream(System.in);
        in.readObject();
        in.close();
    }

    // 恶意对象
    static class EvilObject implements Serializable {
        private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
            in.defaultReadObject();

            // 利用反射获取Runtime类
            Class<?> runtimeClass = Class.forName("java.lang.Runtime");

            // 创建InvocationHandler实现类
            InvocationHandler handler = new InvocationHandler() {
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    if (method.getName().equals("exec")) {
                        // 执行命令
                        Runtime.getRuntime().exec("calc.exe");
                    }
                    return null;
                }
            };

            // 创建代理对象
            Object proxy = Proxy.newProxyInstance(runtimeClass.getClassLoader(), new Class<?>[]{runtimeClass}, handler);

            // 更改反射获取到的Runtime类的私有静态属性
            Field field = runtimeClass.getDeclaredField("currentRuntime");
            field.setAccessible(true);
            field.set(null, proxy);
        }
    }
}
代码将序列化和反序列化放在了一起，实际利用漏洞时可能是分开的。
```

一般实现命令执行的时候有两个方向可以努力：

1. 控制代码、函数：就像命名注入等注入类漏洞一样数据被当作了代码执行；或者重写readObject，加入自定义的代码
2. 控制输入、数据、变量：利用代码中已有的函数和逻辑，通过改变输入内容的形态实现流程的控制(不同的输入会走不同的逻辑流程，执行不同的代码块中的代码)

  对于Java反序列化漏洞来说，这属于控制数据输入一类。在调用反射机制触发漏洞时，有两个基本点必须要满足：

1. 有一个可序列化的类，并且该类是重写了readObject()方法的(由于不存在代码注入，只能查找已有代码逻辑中是否有这样的类)
2. 在重写的readObject()方法的逻辑中有method.invoke函数出现，而且参数可控。

### 反序列化防护
1. 从流量中发现序列化的痕迹，关键字：`ac ed 00 05`，`rO0AB`
2. Java RMI 的传输 100% 基于反序列化，Java RMI 的默认端口是1099端口
3. 从源码入手，可以被序列化的类一定实现了Serializable接口
   - 放在classpath，将应用代码中的java.io.ObjectInputStream替换为SerialKiller，之后配置让其能够允许或禁用一些存在问题的类
   - SerialKiller有HotReload,Whitelisting,Blacklisting几个特性，控制了外部输入反序列化后的可信类型。
4. 观察反序列化时的readObject()方法是否重写，重写中是否有设计不合理，可以被利用之处
5. 对 className 进行白名单校验

```Java

public final class test extends ObjectInputStream{
    ...
    protected Class<?> resolveClass(ObjectStreamClass desc)
            throws IOException, ClassNotFoundException{
         if(!desc.getName().equals("className")){
            throw new ClassNotFoundException(desc.getName()+" forbidden!");
        }
        returnsuper.resolveClass(desc);
    }
      ...
}
```
6. 通过扩展 SecurityManager 禁止 JVM 执行外部命令 Runtime.exec
7. 第三方jar包是否提供了一些公共的反序列化操作接口，是否有相应的安全校验如白名单校验方案

  剩下的内容暂时不写，请看[Java 安全 - 原生反序列化漏洞](https://trganda.github.io/notes/security/java/roadmap/Java-%E5%AE%89%E5%85%A8---%E5%8E%9F%E7%94%9F%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E)和[深入理解 JAVA 反序列化漏洞](https://paper.seebug.org/312/#9)
以后会单独出一章来写Java安全的内容