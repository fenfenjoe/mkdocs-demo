# Lombok

该包的目的：

减少重复代码，重复代码由注解代替。比如get、set方法，构造方法、equals、日志等。


```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.24</version>
    <scope>provided</scope>
</dependency>

```

#### 常见注解

* @Getter

  代替POJO中的get()方法，减少代码；注释在类上。

  

* @Setter

  代替POJO中的set()方法，减少代码；注释在类上。

  

* @Data

  等价于以下几个注解，注解在类上：

  * @Getter
  * @Setter
  * @RequiredArgsConstructor
  * @ToString
  * @EqualsAndHashCode

* @Value

  等价于以下几个注解，注解在类上：

  * @Getter
  * @FieldDefault
  * @AllArgsConstructor
  * @ToString
  * @EqualsAndHashCode
  * @Data

* @Builder

  为实体提供了一种基于建造者模式的构建对象的API；注解在类上。

  ```java
  @Builder
  public class Test{
      private int id;
      private String name;
  }
  public class TestMain{
      public static void main(String[] args){
          //建造者模式构建对象
          Test test = Test.id(2).name("haha").build();
      }
  }
  ```

  

* @SuperBuilder

  与@Builder类似，但@Builder不能使用父类字段构建对象，@SuperBuilder支持。

  

* @AllArgsConstructor

  为实体生成除static之外含所有参数的构造方法；注解在类上。

  

* @NoArgsConstructor

  为实体生成无参构造方法；注解在类上。

  

* @RequiredArgsConstructor

  为实体指定字段（被final或@NonNull修饰）生成构造方法；注解在类上。

  

* @NonNull

  若被标记的变量为空，则自动抛出异常；注解在字段上（方法入参、类的变量）。

* @ToString

  为实体生成默认的toString()方法；注释在类上。

  若某字段不想被打印，则可用@ToString.Exclude修饰。

  ```java
  @ToString
  public class Test{
      @ToString.Exclude
      private String name;
  }
  ```

  

* @EqualsAndHashCode

  hashCode()和equals()方法，原本是通过比较两个对象所在内存地址，来判断是否同一个对象；

  被@EqualsAndHashCode修饰后，重写该两个方法，改为通过所有非静态字段来判断。

  注解在类上；

  与@ToString类似，同样有@EqualsAndHashCode.Exclude和@EqualsAndHashCode.Include

  ```java
  @EqualsAndHashCode
  public class Test{
      @EqualsAndHashCode.Exclude
      private String name;
      @EqualsAndHashCode.Include
      private String id;
  }
  ```

  

* @CleanUp

  修饰在InputStream、OutputStream等需要释放资源的字段上，修饰后不用显式释放(stream.close())





* @CommonsLog /@Flogger /@JBosLog /@Log /@Log4j /@Log4j2 /@Slf4j /@XSlf4j

  日志注解，修饰后的实体可自动生成log对象，不用显式声明；注解在类上。

  

  

  

  

  

  