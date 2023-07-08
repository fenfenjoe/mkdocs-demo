# JAVA各版本特性
[toc]
## JDK1.1
### 反射
* API
    * Class对象
        * 包括
            【注解】Annotations
            【方法】Methods
            【属性】Fields
            【构造器】Constructors
            【接口】Interface
        * 特点
            Declared类型的会返回private、protected、public，但不包括父类中的。
            普通的只会返回公有的（但包括父类中公有的）
    * 动态代理
        * Proxy
```java
 //返回代理对象
public static Object newProxyInstance(
	ClassLoader, //类加载器
	Class<?>[], //动态代理类需要继承的接口
	InvocationHandler //提供方法实现的对象
)
InvocationHandler
				 public Object invoke(
	Object proxy, //提供方法实现的对象
	Method method,//需要执行的方法
	Object[] args//调用参数){
	Object invoke = method.invoke(obj, args);
        return invoke;
}
```
* 基本用法
    * 【获取class文件路径】
    * 【classes文件夹路径】class.getResource("/")
    * 【class具体路径】class.getResource("")
    * 【根据类名加载Class】class.forName(“”)
    
### 内部类

### 多线程

### 序列化/反序列化
* 序列化：将对象转换为二进制数据
* 反序列化：将二进制数据转换为对象
* 需要序列化的类需要继承Serializable接口或Externalnalizable接口
* serialVersionUID
所有需要序列化的类都用到的一个静态常量。
可用于区分某个类的版本。若不需要区分，则需要手动配置该参数；否则在编译时，会根据类的内容生成一个serialVersionUID
序列化/反序列化时，会对类的该属性进行一次判断：如果不一致，则表示两个类对应的版本不一致，序列化失败，抛出异常。

## JDK1.2

### JIT（Just In Time）编译器

## JDK1.4
### 断言

## JDK1.5
### 泛型
#### 规则
* 1.泛型只在编译阶段进行检查。编译后会被擦除
* 2.（1）泛型在属性声明：（List<Integer> arg;）
可以使用泛型边界；
负责限制实例类的泛型参数（只可为Integer）；
（2）泛型在new 实例：（new ArrayList<Integer>();）
不一定要传入泛型实参，若不限定，则可以是任意类型；
不可使用泛型边界；
* 3.泛型通配符：<?>，不限定泛型实参时，实参默认为通配符，即任意类型。
* 4.泛型边界：可以限定泛型参数的上下边界。
限定为某类的子类：<? extends parentClass>；
限定为某类的父类：<? super childClass>
* 5.可以声明多个泛型：<T,K>
* 6.泛型类的泛型在声明时确认；
泛型接口的泛型在声明或者实现时确认；
泛型方法的泛型在调用时确认；
* 7.对于静态方法：静态方法如果需要使用泛型，一定要定义为泛型方法（不可利用泛型类中的泛型）
* 8.协变
    数组是协变的。（ Number是 Integer 的超类型，所以 Number[] 也是 Integer[]的超类型）
    泛型不是协变的。（因为List< Number> 不是 List< Integer> 的超类型，所以在需要 List< Number> 的地方不能传递 List< Integer>）
* 9.PECS原则（Producer Extends Consumer Super）：
定义了泛型上界后只可get不可add；
定义了泛型下界后只可add不可get；
#### 类型
* 泛型类
    
```java
//泛型类的定义
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;
    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }
    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}

//泛型对象声明
Generic<Integer> genericInteger = new Generic<Integer>(123456);
```

* 泛型接口
```java
//泛型接口的定义
public interface Generator<T> {
    public T next();
}

//泛型接口的实现（限定泛型）
public class FruitGenerator implements Generator<String> {
    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};
    @Override
    public String next() {
        Random rand = new Random();
        return fruits[rand.nextInt(3)];
    }
}

//泛型接口的实现（不限定泛型）
class FruitGenerator<T> implements Generator<T>{
    @Override
    public T next() {
        return null;
    }
}

```
    
    
    
* 泛型方法
```java

/**
 * 泛型方法的定义：
 * 泛型方法的基本介绍
 * @param tClass 传入的泛型实参
 * @return T 返回值为T类型
 * 说明：
 *     1）public 与 返回值中间<T>非常重要，可以理解为声明此方法为泛型方法。
 *     2）只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。
 *     3）<T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
 *     4）与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。
 */
public <T> T genericMethod(Class<T> tClass)throws InstantiationException ,
  IllegalAccessException{
        T instance = tClass.newInstance();
        return instance;
}

/**
* 与泛型类中的普通成员方法的区别
* 泛型类中的普通成员方法，可能会返回泛型类的值。
* 如
* public T get(){};
* 但只有在 public和T中间，加上<T>,才是泛型方法。
* 即public <T> T get(T arg0){};
**/
```

### 注解

### 自动装箱、自动拆箱


## JDK1.8

### Lambda表达式

Lambda表达式，它允许我们将函数当成参数传递给某个方法，或者把代码本身当作数据处理。

#### Lambda表达式示例

```java

/**
* 匿名函数：无入参，只有一句代码，返回字符串
**/
() -> "Hello World";

/**
* 匿名函数：有一个字符串e入参，只有一句代码，但无返回值
**/
e ->  System.out.println(e);

/**
* 匿名函数：有两个入参，有多行代码（需要用大括号括住），返回字符串
**/
(String a,String b) -> { System.out.println("a="+a+"b="+b); a+b; }

/**
* 方法引用，等同于：(String a)  -> { System.out.println(a); }
**/
System.out::println

/**
* 方法引用，等同于：Supplier<Student> s = () -> new Student();
**/
Supplier<Student> s = Student::new;

```

#### Lambda表达式常见用法
```java
//1. JDK1.8的list的forEach函数入参可以接收Lambda表达式
//等于打印list里的所有字符串
List features = Arrays.asList("Lambdas", "Default Method", "Stream API", "Date and Time API"); 
features.forEach(n -> System.out.println(n));

//等同于（该写法也是1.8的新特性，名为方法引用）
features.forEach(System.out::println);

//2.Stream
详见下面对Stream的介绍

//* Supplier作为入参

//* Consumer作为入参

//* Predicate作为入参




```


#### 规则
1. Lambda的函数体内，可以访问传给它的变量，也可以访问它外部的变量（但不能修改），以下是反例：
```java
//以下代码会编译报错。
List<Integer> primes = Arrays.asList(new Integer[]{2, 3,5,7});
int factor = 2;
primes.forEach(element -> { factor++; });

```

#### 概念
##### 函数式接口

Lambda的设计者们为了让现有的功能与Lambda表达式良好兼容，考虑了很多方法，于是产生了函数接口这个概念。
函数接口指的是只有一个函数的接口，这样的接口可以隐式转换为Lambda表达式。



## Java 8 Stream
### 什么是Stream
通过Stream，可以编写出一种类似于SQL风格的代码，用于数据的查询、过滤、排序等。

```java
//示例： 
List<Integer> transactionsIds = 
widgets.stream()
             .filter(b -> b.getColor() == RED)
             .sorted((x,y) -> x.getWeight() - y.getWeight())
             .mapToInt(Widget::getWeight)
             .sum();
```



