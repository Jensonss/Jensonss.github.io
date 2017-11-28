浅谈Java注解



# 前言

一直以来对于Java注解的理解都停留在`@Override`的使用上面。对于其他则一无所知，但是慢慢发现很多框架都使用了注解功能，越来越觉得有必要了解下注解的实现了。

对于注解的学习可以按照图下的四个步骤：



![学习四步](http://othg5ggzi.bkt.clouddn.com/%E5%AD%A6%E4%B9%A0%E5%9B%9B%E6%AD%A5.png)

**知道**就是了解什么是注解？它有什么用？

**使用**就是初级的使用方法

**自定义**就是根据需求定义自己的注解实现

**原理**就是介绍下注解如何实现的

下面分别来介绍下每步

# 知道

## 什么是注解

Java注解从5.0版本推出，

注解就是对类、方法、参数、变量、构造器及包声明中的特殊修饰符，实现对元数据的描述。

元数据是什么？

简单点说元数据就是用来存储数据的数据，比如用来描述学生信息需要定义一个Student类，那么Student类用来描述学生信息，但是谁又来描述Student类本身呢？就是元类了(meta class)，即Java中Class类。

## 注解作用

其实在注解出现之前，它的功能也有其他一些方法来实现，但是那个时候的实现是比较随意的，由开发人员自行实现。所以注解的出现相当于统一了标准。

# 使用

## 内置标准注解

Java注解在推出时便内置了三种标准注解：

`@Override`，说明当前方法覆盖父类的方法实现。

`@Deprecated`，说明该代码已经废弃了，不建议使用，如果使用了编译器会发出警告。

`@SuppressWarnings`，说明关闭当前修饰代码段内的编译器警告信息。

使用方法如下：

![内置注解](http://othg5ggzi.bkt.clouddn.com/%E5%86%85%E7%BD%AE%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8.png)



可以清楚看到标记了`@Deprecated`的方法会被划线来表示已经过期废弃。

而`test()`和`test1()`两个方法同样的实现，因为`test1()`标记了`@SuppressWarnings`而没有出现黄色警告信息。

`@Override`更不用说，如果父类没有该方法就出现错误提示的。

# 自定义

## 元注解

上面简单介绍了Java内置的三种标准注解，但是这三种根本不够看的，我们需要实现自己的注解。

但是在自定义注解之前需要了解一个知识点：元注解。

上面有说到描述类的类叫元类 ，所以这里描述注解的注解自然就是元注解。

Java提供了四种元注解用来帮助自定义新注解：

| @Document  | 将注解信息加入Javadoc文档 |
| ---------- | ---------------- |
| @Inherited | 是否允许子类继承本注解      |
| @Retention | 注解存活日期           |
| @Target    | 注解的作用目标          |

其中`@Retention`有三个可选值：

| RetentionPolicy.SOURCE  | 存在于编译阶段                         |
| ----------------------- | ------------------------------- |
| RetentionPolicy.CLASS   | 存在于字节码文件中，但是类加载时候不会用到。这是默认的注解方式 |
| RetentionPolicy.RUNTIME | 存在于整个运行期，可以通过反射拿到注解信息           |

网上关于`RetentionPolicy.SOURCE`的说法有说“在编译阶段丢弃”,一时让我很费解，既然在编译阶段就丢弃了，那`@override`重写标记也是编译阶段丢弃，但是它又如何实现父类没有该方法时出现编译错误提示呢？所以我觉得对于`RetentionPolicy.SOURCE`的描述还是用**仅存在于编译阶段**更准确一些。

关于`@Target`也有多个属性值可选：

| ElementType.TYPE           | 类、接口或enum |
| -------------------------- | --------- |
| ElementType.FIELD          | 实例变量      |
| ElementType.METHOD         | 方法        |
| ElementType.PARAMETER      | 参数        |
| ElementType.CONSTRUCTOR    | 构造器       |
| ElementType.LOCAL_VARIABLE | 局部变量      |
| ElementType.PACKAGE        | 包         |

关于元注解已经说完了，下面开始元注解实战

## 实战元注解

先看下需求：

现在从服务器返回一个json串{age:29,name:Jenson}

我在本地已有实体类，代码如下:

```java
public class People {
	private String pName;
	private int pAge;

	public String getpName() {
		return pName;
	}

	public void setpName(String pName) {
		this.pName = pName;
	}

	public int getpAge() {
		return pAge;
	}

	public void setpAge(int pAge) {
		this.pAge = pAge;
	}

}
```

现在我们要设计一个JsonParser类，通过方法JsonParser.parse(String json, People.class)；可以直接解析成功并返回一个People对象。

那么问题来了，本地类的属性和json的key值不一样，我们如何通过注解实现json的属性和People实例属性一一对应呢？

我希望通过给People的实例属性添加注解来达到一致：

```java
	@json("name")
	private String pName;
	@json("age")
	private int pAge;

```

在注解中的值为json中的key值。接下来看看注解json的实现：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(value = ElementType.FIELD)
public @interface json {
	String value() default "";
}
```

因为是给People**属性**设置注解所以使用`@Target(value = ElementType.FIELD)`，

因为要用到**反射**，所以设置周期为`@Retention(RetentionPolicy.RUNTIME)`。

下面看JsonParser.parse的实现：

```java
@SuppressWarnings(value = { "unchecked" })
	public static Object parse(String json, Class clz) throws Exception {
		Class clss = clz;
		Object obj = clss.newInstance();
		for (Field f : clss.getDeclaredFields()) {
			Json j = f.getAnnotation(Json.class);
			String key = j.value();
			JSONObject jo = JSONObject.fromObject(json);
			Method method = clss.getDeclaredMethod("set" + f.getName(), new Class[] { f.getType() });
			method.invoke(obj, jo.get(key));
		}
		return obj;
	}

```

因为通过注解绑定了People的属性和json的key值。所以通过反射拿到注解信息的value，通过这个value作为key拿到json的value。最后通过set方法把json的value赋值给对应的属性。这样完成了解析过程。

这样看起来好像比直接使用JSONObject解析麻烦，但是其实这样做更灵活，而且一般服务器交互过程都是若干个实体类，而我们只要实现这一次就可以达到通用的目的。

其实看下jackson和gson等解析应该都是类似的方法。另外注解在hibernate和各种测试框架都有广泛应用。

最后看看main方法实现：

```java
public static void main(String[] args) {
		String json = "{age:29,name:'Jenosn'}";
		try {
			People p = (People) JsonParser.parse(json, People.class);
			System.out.println("年龄：" + p.getpAge());
			System.out.println("姓名：" + p.getpName());
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
```

打印结果：

```
年龄：29
姓名：Jenosn
```





# 原理

