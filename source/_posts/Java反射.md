title: Java反射
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-12-18 18:37:00
---
## 注解
### Java注解概述
注解也叫元数据。一种代码级别的说明。它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明注释。

### 注解分类
- 标记注解： 没有参数的注解，仅用自身的存在与否为程序提供信息，如`@Override`注解，该注解没有参数，用于表示当前方法为重写方法。
- 单值注解： 只有一个参数的注解，如果该参数的名字为value，那么可以省略参数名，如 `@SuppressWarnings(value = "all")`，可以简写为`@SuppressWarnings("all")`。
- 完整注解： 有多个参数的注解。
- 自定义注解：使用反射机制解析注解


### 注解作用
1. Java中是有三种注释的，分别为单行注释、多行注释和文档注释。@开头的元注解，这就是基于文档注释的注解。
2. 使用反射来通过代码里标识的元数据对代码进行分析
3. 用来做特定的编译检查，为代码提供了一种规范制约，避免我们后续在代码中处理太多的代码以及功能的规范

### 常见注解
【内置注解】
1. @Deprecated – 所标注内容不再被建议使用；
2. @Override – 只能标注方法，表示该方法覆盖父类中的方法；
3. @SuppressWarnings – 所标注内容产生的警告，编译器会对这些警告保持静默；
4. @interface – 用于定义一个注解；
5. @Documented --将所标注内容包含到javadoc中；
6. @Inherited – 只能被用来标注“Annotation类型”，它所标注的Annotation具有继承性，跟类的继承形式同意；
7. @Retention – 只能被用来标注“Annotation类型”，而且它被用来指定Annotation的RetentionPolicy属性,它表示注解存在阶段是保留在源码（编译期），字节码（类加载）或者运行期（JVM中运行）

【元注解】
1. @Target:用于描述注解的使用范围即被描述的注解可以用在什么地方
2. @Retention:表示需要在什么级别保存该注释信息，用于描述注解的生命周期(SOURCE < CLASS < RUNTIME)
3. @Document: 说明该注解将被包含在javadoc中
4. @Inherited:说明子类可以继承父类中的该注解

【自定义注解】
1. @interface用来声明一个注解，格式:public @interface 注解名{定义内容}
2. 其中的每一个方法实际上是声明了一个配置参数
3. 方法的名称就是参数的名称
4. 返回值类型就是参数的类型(返回值只能是基本类型,Class,String,enum)
5. 可以通过default来声明参数的默认值
6. 如果只有一个参数成员，一般参数名为value
7. 注解元素必须要有值，我们定义注解元素时，经常使用空字符串，0作为默认值


## 反射
### 静态语言和动态语言
**动态语言**
运行时可以改变其结构的语言:例如新的函数、对象、甚至代码可以被引进，已有的函数可以被删除或是其他结构上的变化。通俗点说就是在运行时代码可以根据某些条件改变自身结构。主要动态语言: Obiect-C、C#、JavaScript、PHP、Python等
**静态语言**
与动态语言相对应的，运行时结构不可变的语言就是静态语言。如Java、C、C++

### 反射概念
反射机制允许程序在执行期借助于Reflection API取得任何类的内部信息，并能直接操作任意对象的内部属性及方法。
```Java

Class c = Class.forName(“java.lang.String”)
```
加载完类之后，在堆内存的方法区中就产生了一个Class类型的对象(一个类只有这个对象就包含了完整的类的结构信息。
我们可以通过这个Class对象看到类的结构。这个对象就像一面镜子，透过这个镜子看到类的结构，所以我们形象的称之为:反射
<a href="https://smms.app/image/g2HYe3Cw6tSKoPz" target="_blank"><img src="https://s2.loli.net/2023/12/18/g2HYe3Cw6tSKoPz.jpg" ></a>

### 反射的功能
1. 在运行时判断任意一个对象所属的类
2. 在运行时构造任意一个类的对象
3. 在运行时判断任意一个类所具有的成员变量和方法
4. 在运行时获取泛型信息
5. 在运行时调用任意一个对象的成员变量和方法
6. 在运行时处理注解
7. **生成动态代理（重要）**

### 常用Class类反射方法（如何使用反射）
<a href="https://smms.app/image/knT4QSyE8XI6h5t" target="_blank"><img src="https://s2.loli.net/2023/12/18/knT4QSyE8XI6h5t.jpg" ></a>

##### 获取Class对象
```Java

public class Apple {
		......
}
Class<Apple> clazz;

// 1. 直接.class
clazz = Apple.class;

// 2. 通过 类的全路径
clazz = (Class<Apple>) Class.forName("collection.Apple");

// 3. 类加载器加载
clazz = (Class<Apple>) Thread.currentThread().getContextClassLoader().loadClass("collection.Apple");
```

##### 获取字段
```Java

// 获取所有字段 getDeclaredFields
Arrays.asList(Apple.class.getDeclaredFields()).forEach(f->System.out.println(f.getName()));

// 获取单个字段
Apple.class.getDeclaredField("area").getName();
```

##### 修改字段值
```Java

Apple apple = new Apple();
apple.setArea("深圳");
// 获取area字段
Field field = Apple.class.getDeclaredField("area");

	***  	// private字段，需要强制
	***  	field.setAccessible(true);

// 将值修改成长沙
field.set(apple, "长沙");
System.out.println(apple.getArea());  //长沙
```

##### 通过反射创建类对象
通过反射创建类对象主要有两种方式：通过 Class 对象的 newInstance() 方法、通过 Constructor 对象的 newInstance() 方法。

**第一种：通过 Class 对象的 newInstance() 方法。**
```Java

Class clz = Apple.class;
Apple apple = (Apple)clz.newInstance();
```

**第二种：通过 Constructor 对象的 newInstance() 方法**
```Java

Class clz = Apple.class;
Constructor constructor = clz.getConstructor(String.class, int.class);
Apple apple = (Apple)constructor.newInstance("红富士", 15);
```

通过 Constructor 对象创建类对象可以选择特定构造方法，而通过 Class 对象则只能使用默认的无参数构造方法。

##### 获取方法
我们通过 Class 对象的 getFields() 方法可以获取 Class 类的属性，但无法获取私有属性。
```Java

Class clz = Apple.class;
Field[] fields = clz.getFields();
for (Field field : fields) {
    System.out.println(field.getName());
}

输出：price
```

而如果使用 Class 对象的 getDeclaredFields() 方法则可以获取包括私有属性在内的所有属性：
```Java

Class clz = Apple.class;
Field[] fields = clz.getDeclaredFields();
for (Field field : fields) {
    System.out.println(field.getName());
}

输出：
name
price
```
与获取类属性一样，当我们去获取类方法、类构造器时，如果要获取私有方法或私有构造器，则必须使用有 declared 关键字的方法。

##### 执行方法
```Java

Apple apple = new Apple();
apple.setArea("深圳");
// 获取setArea 方法
Method m = Apple.class.getDeclaredMethod("setArea", String.class);

// 调用apple对象的 setArea
m.invoke(apple, "长沙");
System.out.println(apple.getArea()); // 输出长沙
```

##### 获取继承的类，接口
```Java

// 获取 继承的类
Apple.class.getSuperclass()

// 获取实现的接口
Apple.class.getInterfaces()

注意如果是几代继承就获取不到。比如A 实现B ，B实现C 。 
获取A的getInterfaces就只能返回B ，而不返回C 。
```

### 示例--反射实现SPI机制
**通过改配置文件，来实现不同功能的切换。**
##### 定义两种功能（RedisCache和MemeCache）
```Java

//缓存接口
public interface Cache {
	// 设置缓存
	void set(String key , String value);
	//获取缓存
	String get(String key);
}

//redis 提供的缓存实现
public class RedisCache implements Cache{
	@Override
	public void set(String key, String value) {
		System.out.println("[redis缓存] set");
	}
	@Override
	public String get(String key) {
		System.out.println("[redis缓存] get");
		return "";
	}
}

//MemeCache 提供的缓存实现
public class MemeCache implements Cache{
	@Override
	public void set(String key, String value) {
		System.out.println("[MemeCache缓存] set");
	}
	@Override
	public String get(String key) {
		System.out.println("[MemeCache缓存] get");
		return "";
	}
}
```

##### spring.factory配置文件
```factory

# collection.RedisCache 是RedisCache类的全路径，代表项目要用redis缓存
collection.RedisCache
```

##### ServiceLoader实现功能
```Java

public class ServiceLoader {
	// 读取spring.factorys文件 
	private static Map<Class<?>, Class<?>> doFind() throws IOException {
		Path f = Paths.get(ServiceLoader.class.getResource("/").getPath().substring(1), "META-INF/spring.factorys");
		List<String> strs = Files.readAllLines(f, Charset.forName("UTF-8"));
		final Map<Class<?>, Class<?>> map = new HashMap<>();
        
        // 把行内容变成 Map key： 接口 value:对应的实现类 ，再获取实现的接口
		for (String line : strs) {
			try {
				Class<?> impl = Class.forName(line);
				map.put(Class.forName(line).getInterfaces()[0], impl);
			} catch (Exception e) {
			}
		}
		return map;
	}
	
	@SuppressWarnings("unchecked")
	public static <T> T load(Class<T> inter) throws Exception {
    
		// 加载 spring.factorys文件
		Map<Class<?>, Class<?>> mapper = doFind();
        
		// 查找 对应的实现类
		Class<T> impl = (Class<T>) mapper.get(inter);
		if (impl == null) {
			return null;
		}
        
		// 否则 利用反射生成 实现
		return doCreate(impl);
	}
    
	// 反射实例化对象
	private static <T> T doCreate(Class<T> impl) throws InstantiationException, IllegalAccessException {
		return impl.newInstance();
	}
}
```

##### 代码逻辑
1. 利用Paths加载${项目}/META-INF/spring.factorys文件，找到具体用哪个实现类。
2. 将上述实现类forClass得到Class对象，并通过getInterfaces获取其实现的接口，封装成Map<接口Class，实现类Class>。
3. 匹配load方法用户传过来的接口，通过上面map得到实现类，通过newInstance方法构造实例返回。

### Java内存加载
<a href="https://smms.app/image/p5Yl7cxBDjRsor1" target="_blank"><img src="https://s2.loli.net/2023/12/20/p5Yl7cxBDjRsor1.jpg" ></a>

<a href="https://smms.app/image/7q1EmModVgfTpaH" target="_blank"><img src="https://s2.loli.net/2023/12/20/7q1EmModVgfTpaH.jpg" ></a>

1. 类的加载 (Load) 类加载器完成将类的class文件读入内存并创建`java.lang.Class`对象操作。
2. 类的链接 (Link) 将类的二进制数据合并进JRE，在合并的过程中可以对类进行校验，检查其是否存在安全问题，是否符合JVM语法规范，接着为类变量 (static) 分配内存和设置默认初始值，这些内存在方法区中进行分配。最后在虚拟机,中将常量名替换为引用地址。
3. 类的初始化 (lnitialize) JVM对类进行初始化，过程中执行类构造器的方法，此方法是编译期自动收集类中的变量赋值动作和静态代码合并而成的，目标虚拟机会保证类构造器的方法会在多线程中被正确的加锁和同步。且在初始化过程中，如果发现类的父类还没有被初始化，则会优先初始化其父类。

**类缓存：**标准的JavaSE类加载器可以按要求查找类，但一旦某个类被加载到加载器中将维持加载（缓存）一段时间，不过JVM垃圾回收机制可以回收这些Class对象

## Java反射安全
### 反射带来的安全问题
1. 性能低-因为java反射动态地解析类型，它涉及处理像扫描类路径找到要加载的类，导致性能降低。 
2. 安全限制-反射需要运行时权限，可能不适用于在安全管理器下运行的系统。这可能会导致应用程序在运行时由于安全管理器而失败。 
3. 安全问题-使用反射，我们可以访问我们不应该访问的部分代码，例如，我们可以访问类的私有字段并更改它的值。这可能是一个严重的安全威胁，并导致您的应用程序的行为异常。 
4. 高维护-反射代码很难理解和调试，在编译时也无法找到代码的任何问题，因为类可能不可用，使得它不太灵活和难以维护。

### 安全问题原理
每个类都有且仅有一个class对象。通过类和对象都能获取到类的class对象，获取到class对象有下面三种方式。

    1.Student.class   //通过类名.class的方式获取class对象

    2.stu1.getClass()   //通过类对象.getClass()的方式获取class对象

    3.Class.forName("com.test.Student")  //通过全限定名的方式获取class对象

Java反射的主要作用是通过class对象来对类的属性和方法进行获取和调用，但是还有一个极为重要的特征就是可以调用类的私有方法（包括protected和private）。这种的特性是后续很多java反序列化利用链依赖反射机制的重要原因。