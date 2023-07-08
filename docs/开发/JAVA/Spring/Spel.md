# SPEL



SPEL，是Spring Expression的缩写，Spring表达式。



**如何使用&哪些地方会使用**

```java
/**
* 编码的方式解析SPEL
**/
public void parse(){
    String spel = ""; //SPEL表达式
    
    //定义SPEL解析器
    SpelExpressionParser parser = new SpelExpressionParser();
    
    //使用解析器解析表达式
    Expression exp = parser.parseExpression(spel);
    
    //获取解析结果
    String value =(String)exp.getValue();
    
    //打印结果
    System.out.println(value);
    
    
}
```











**SPEL可以解析些什么？**

* 字符串转基本类型（double、int、boolean、null值）



```java
public void parse(){
    //定义SPEL解析器
    SpelExpressionParser parser = new SpelExpressionParser();
    
    //解析字符串
    {
        Expression exp = parser.parseExpression("'spel'");
    	String value =(String)exp.getValue();
    	System.out.println(value);
    }
    
    //解析浮点数
    {
        Expression exp = parser.parseExpression("6.0221415E+23");
    	double value =(Double)exp.getValue();
    	System.out.println(value);
    }
    
    //解析布尔值
    {
        Expression exp = parser.parseExpression("true");
    	boolean value =(Boolean)exp.getValue();
    	System.out.println(value);
    }
    
    //解析null值
    {
        Expression exp = parser.parseExpression("null");
    	Object value =exp.getValue();
    	System.out.println(value);
    }
}
```



* 对象的访问、方法的访问、数组的访问、Map的访问

```java
/**
* 假设有以下对象 class Student { name, province}
* 对象属性访问: name
* 对象属性安全访问（不会抛空指针）：.?province.?city.?name
* 方法调用：setCity('Guangdong')
* 对象数组属性访问： parents[2]
* List访问：[2]
* Map访问：['key']
**/
public void parse(){
    //定义SPEL解析器
    SpelExpressionParser parser = new SpelExpressionParser();
    //评估上下文：当前表达式操作的对象
    SimpleEvaluationContext context = SimpleEvaluationContext.forReadOnlyDataBinding().build();
    
    //对象属性访问（获取student对象的name属性）
    {
        Student student = new Student("John");
        Expression exp = parser.parseExpression("name");
    	String value = exp.getValue(context,student,String.class);
    	System.out.println(value);
    }
    
    //数组访问（获取student对象的parents数组）
    {
        Student student = new Student("John");
        student.setParents(new String[]{"John","Amy","Lucy"});
        Expression exp = parser.parseExpression("parents[2]");
    	String value = exp.getValue(context,student,String.class);
    	System.out.println(value);
    }
    
    //List访问（获取list某个下标的对象）
    {
        List<String> list = new ArrayList({"John","Amy","Lucy"});
        Expression exp = parser.parseExpression("[2]");
    	String value = exp.getValue(context,list,String.class);
    	System.out.println(value);
    }
}
```



* 新建List、Map



```java
/**
* 新建List：{1,3,4,7}
* 新建Map：{'name':'John','age':22}
* 新建数组：new int[4]
**/
public void parse(){
    
}
```



* 集合元素筛选



```java
/**
* 筛选List中，国籍为"中国"的元素： members.?[nationality == '中国']
* 取List中，第一个国籍为"中国"的元素： members.^[nationality == '中国']
* 取List中，最后一个国籍为"中国"的元素： members.$[nationality == '中国']
* 取Map中，value>200的Entry： myMap.?[value>200]
**/
public void parse(){
    
}
```



* 集合映射



```java
/**
* List<Student>映射为List<String>：.![name]
* Map映射为List: .![key +'-' + value]
**/
public void parse(){
    
}
```



* 关系运算、逻辑运算、instanceof



```java
/**
* 2 == 2  （关系运算）
* 2 > 5  （又可以写作 2 gt 5）
* 2 !=5 and 3<4 （关系运算+逻辑运算）
* !true （逻辑运算）
* 'xxx' instanceof T(Integer)  （instanceof）
**/
public void parse(){
    
}
```



* 数学运算



```java
/**
* 2 + 2  
* 2 - -2
* 2 - 1e4
* 2 * -3
* 6 / 3
* 7 % 4
* 1+2-3/4
**/
public void parse(){
    
}
```



* 赋值操作、三元运算符



```java
/**
* name = '' （赋值操作）
* name!=null? name :'i have no name'  （三元运算符）
**/
public void parse(){
    
    
}
```



* Class对象、静态方法调用



```java
/**
* 获取Class对象：T(String)  （java.lang）中的类可以省略包名
* 获取Class对象：T(java.util.Date) 
* 调用类的静态方法： T(Long).parseLong('9999')
**/
public void parse(){
    //定义SPEL解析器
    SpelExpressionParser parser = new SpelExpressionParser();
    
    {
        Expression exp = parser.parseExpression("T(String)");
    	Class value = exp.getValue(context,student,Class.class);
    	System.out.println(value);
    }
    
    {
        Expression exp = parser.parseExpression("T(Long).parseLong('9999')");
    	Long value = exp.getValue(context,student,Long.class);
    	System.out.println(value);
    }
    
}
```





* 表达式模板：#{}，即在字符串中包含了SPEL表达式



```java
/**
* 含表达式的字符串：随机数字是 #{T(java.lang.Math).random()}
* 含入参的字符串：我的名字是： #myName
**/
public void parse(){
    //定义SPEL解析器
    SpelExpressionParser parser = new SpelExpressionParser();
    //评估上下文：当前表达式操作的对象
    SimpleEvaluationContext context = SimpleEvaluationContext.forReadWriteDataBinding().build();
    
    {
        Expression exp = parser
        .parseExpression("随机数字是 #{T(java.lang.Math).random()}",
                        new TemplateParserContext());
        String value = exp.getValue(String.class);
        System.out.println(value);
    }
    
    {
        context.setVariable("myName","John")
        Expression exp = parser
        .parseExpression("我的名字是： #myName}",
                        new TemplateParserContext());
        String value = exp.getValue(String.class);
        System.out.println(value);
    }
    
}
```

